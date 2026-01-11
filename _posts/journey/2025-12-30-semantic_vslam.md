---
layout: post
title: "Semantic VSLAM"
subtitle: "VSLAM + YOLO with 3 stereo cameras"
tags: [drone, vslam, yolo, semantic, ue5, ros2, rviz2]
author: solgyu
category: journey
subcategory: dev
---

I believe VSLAM is going to be more dominant in computer vision. LiDAR is actually the most popular sensor for robotic mapping, but the problem is that LiDAR is very expensive. In contrast, VSLAM doesn't require expensive LiDAR—it only needs cameras that are even found in your phone. The collaboration of multiple cameras and IMU sensors can create a map. The reason VSLAM is even better than SLAM using LiDAR is that you can utilize these cameras for more than just making maps—you can use them for recording videos too. Isn't that awesome?

However, there are still some limitations to VSLAM. LiDAR shoots many beams into the environment and calculates the reflected beams to create a map. In contrast, cameras have to estimate depth from images and transform them into a map. Honestly, VSLAM can't make as sophisticated a map compared to LiDAR, so we need other technologies to complement these limitations.

Deep learning is the key. Some brilliant researchers thought, "What if we combined YOLO and VSLAM?" The lack of sophistication can be fulfilled by YOLO. And it worked!

![Semantic VSLAM Pipeline](/assets/img/semantic_vslam_pipeline.png)
*Semantic VSLAM pipeline overview*

## Tech Stack

- **RTAB-Map** for RGB-D SLAM backend
- **OpenCV StereoSGBM** for calculating Disparity
- **WLS Filter** for refining Disparity
- **robot_localization** for EKF sensor fusion
- **YOLOv8n** for object detection
- **TF2** for transforming coordinates

---

## 1. Stereo Depth Estimation

The essential part is how to estimate depth from stereo images. The core formula is surprisingly simple:

```
depth = (fx × baseline) / disparity
```

Here's what each term means:
- **fx** = 554.25 (focal length in pixels)
- **baseline** = 0.12m (distance between the two cameras)
- **disparity** = how much a pixel shifts between left and right images

Think of it this way: when you look at something close, your left eye and right eye see it at very different positions. But when you look at something far away, both eyes see it almost at the same position. That "position difference" is disparity, and it tells us how far away objects are.

With my camera setup, the depth range works out to:
- **Minimum depth** (disparity=128): about 0.52m
- **Maximum depth** (disparity=1): about 66m
- **Practical range**: 0.3m ~ 15m

---

## 2. StereoSGBM Algorithm

Semi-Global Block Matching does three important jobs:

### 2.1 Block Matching
I use front_left and front_right cameras, so I constantly get 2 images. The algorithm compares similarity between them using a `blockSize × blockSize` window. It's like sliding a small square across both images asking "does this patch look the same?"

### 2.2 Cost Function
For each pixel, we calculate how "expensive" each disparity guess is:

```
E(D) = Σ(C(p, D_p) + Σ P1·T[|D_p - D_q| = 1] + P2·T[|D_p - D_q| > 1])
```

The P1 and P2 terms are penalties. P1 penalizes small disparity changes (keeps things smooth), and P2 penalizes big jumps (prevents wild guesses). In my setup:
- **P1** = 8 × 3 × blockSize² (small change penalty)
- **P2** = 32 × 3 × blockSize² (big jump penalty)

### 2.3 Semi-Global Optimization
Instead of just looking at one direction, the algorithm accumulates costs along 8 different paths (up, down, left, right, and diagonals). This makes the depth map much more consistent than simple block matching.

---

## 3. WLS Filter: Making Depth Maps Clean

Raw disparity maps are noisy. The WLS (Weighted Least Squares) filter solves this optimization problem:

```
minimize: Σ(u_i - d_i)² + λ Σ w_ij(u_i - u_j)²
```

In plain English: "Make the output similar to the input, but also smooth—except don't smooth across edges." The **λ = 8000** controls how aggressive the smoothing is, and **σ_color = 1.5** controls edge sensitivity.

The result? Depth maps that are smooth in uniform areas but preserve sharp edges at object boundaries.

---

## 4. 3D Back-Projection

Once we have depth, we need to convert 2D pixel coordinates to 3D world coordinates. This is the pinhole camera model in reverse:

```
X = (px - cx) × depth / fx
Y = (py - cy) × depth / fy
Z = depth
```

Where (cx, cy) is the optical center and (fx, fy) are focal lengths. This is how we figure out where objects actually are in 3D space.

For detected objects, we can estimate real-world size:
```
object_width = bbox_width × depth / fx
object_height = bbox_height × depth / fy
```

So if YOLO detects a person, we can tell not just "there's a person" but "there's a person 2.5 meters away who's about 1.7m tall."

---

## 5. Extended Kalman Filter (EKF) Fusion

VSLAM alone isn't enough for a drone. Visual odometry runs at maybe 10-15Hz and can drift. IMU runs at 250Hz and doesn't drift but has noise. EKF combines them into something better than either alone.

### State Vector (15 dimensions)
```
x = [x, y, z, roll, pitch, yaw, vx, vy, vz, ωx, ωy, ωz, ax, ay, az]ᵀ
```
Position, orientation, velocity, angular velocity, and acceleration—everything we need to know where the drone is and how it's moving.

### The Two-Step Dance

**Prediction (when IMU arrives at 250Hz):**
```
x̂_predicted = f(x̂_previous, IMU_data)
P_predicted = F × P_previous × Fᵀ + Q
```

**Update (when VSLAM arrives at 10-15Hz):**
```
K = P_predicted × Hᵀ × (H × P_predicted × Hᵀ + R)⁻¹
x̂_updated = x̂_predicted + K × (measurement - expected)
P_updated = (I - K × H) × P_predicted
```

The Kalman gain K is the magic sauce. It decides: "Should I trust this VSLAM measurement or my IMU prediction more?" If VSLAM is confident, K is large. If VSLAM is noisy, K is small.

The output? Smooth 50Hz pose estimates that combine the best of both sensors.

---

## 6. Coordinate Frame Transformation

Here's a gotcha that took me a while to figure out: ROS and PX4 use different coordinate systems.

- **ROS (FLU)**: x-forward, y-left, z-up
- **PX4 (FRD)**: x-forward, y-right, z-down

To convert, we rotate 180° around the x-axis:

```
# Position transformation
x_px4 = x_ros
y_px4 = -y_ros  (flip sign)
z_px4 = -z_ros  (flip sign)

# Quaternion transformation
R_convert = Rotation.from_euler('x', 180, degrees=True)
q_px4 = R_convert × q_ros × R_convert.inverse()
```

Without this, your drone thinks "up" is "down" and things get exciting very quickly.

---

## 7. RTAB-Map Visual Odometry

RTAB-Map handles the SLAM backend. Here's how it works:

### Feature Extraction
Uses GFTT (Good Features To Track) + BRIEF descriptors. I set it to extract up to 5000 features per frame with a minimum of 5 inliers required for a valid pose estimate.

### Motion Estimation
Frame-to-Map matching (Odom/Strategy: 0):
1. Extract features from current frame
2. Match against accumulated map features
3. Use PnP algorithm to estimate camera pose
4. RANSAC removes outliers

### Loop Closure Detection
When the drone returns to a previously visited place, RTAB-Map detects it using Bag-of-Words visual similarity. This corrects accumulated drift and keeps the map consistent.

---

## 8. Semantic Mapping Pipeline

Here's where YOLO meets VSLAM:

```
RGB Image → YOLO → 2D Bounding Boxes
                      ↓
              + Depth Image
                      ↓
              3D Position (Back-projection)
                      ↓
              SemanticObject
                      ↓
         ┌───────────┴───────────┐
         ↓                       ↓
   RViz MarkerArray      Central LLM (JSON)
```

### Depth Sampling Strategy
Raw depth at the exact center of a bounding box can be noisy. Instead, I sample a 10×10 pixel region around the center and take the median of valid depths. This robust approach ignores outliers and gives a more reliable distance estimate.

```python
depth_region = depth_image[y1:y2, x1:x2]
valid_depths = depth_region[(depth_region > 0.3) & (depth_region < 15.0)]
depth = np.median(valid_depths)
```

---

## 9. Testing in UE5

I tested the entire pipeline in Unreal Engine 5 using a car dealer asset. Why a car dealer? It's packed with visual features—cars, signs, windows, reflections—which is exactly what VSLAM needs. The more distinct features an environment has, the more accurate your depth estimation and pose tracking become. Featureless environments like blank walls or open fields are VSLAM's worst nightmare.

<video width="100%" controls>
  <source src="/assets/img/semantic_vslam_demo.webm" type="video/webm">
  Your browser does not support the video tag.
</video>
*Semantic VSLAM demo: drone navigation with real-time 3D mapping and object detection*

In the demo, you can see the drone building a 3D map while flying around. YOLO detects objects (cars, people, etc.) and projects them into 3D space. The result is a semantic 3D map that tells you not just "there's something at coordinates (x, y, z)" but "there's a car at (x, y, z)."

---

## Summary

This Semantic VSLAM system combines several technologies into a coherent pipeline:

1. **Stereo → Depth**: StereoSGBM + WLS filter generates high-quality depth maps
2. **Visual Odometry**: RTAB-Map RGB-D mode provides stable pose estimation
3. **Sensor Fusion**: EKF combines VSLAM (10Hz) + IMU (250Hz) → smooth 50Hz output
4. **Semantic Mapping**: YOLO detection + depth projection → 3D semantic objects
5. **PX4 Integration**: FLU→FRD coordinate transformation for drone control

I mentioned Tesla earlier as an example—they use Occupancy Networks, but the core idea is similar: multiple cameras + AI = understanding the 3D world without expensive LiDAR.

Next step: integrating Semantic VSLAM with GPS to get even more precise 3D maps. VSLAM handles local accuracy while GPS provides global positioning—combining them should eliminate long-term drift and enable mapping across larger areas.
