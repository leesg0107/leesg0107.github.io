---
layout: post
title: OpenAI-Swarm 
subtitle: multiagent-communication 
cover-img: /assets/img/path.jpg
thumbnail-img: /assets/img/post_swarm/swarm_bee.png
share-img: /assets/img/path.jpg
tags: [MA]
author: OpenAi 
---

### openai again.. 또 너야 
학교 공부 정말 싫어하지만 최소한의 학점을 위해 시험 공부를 하던 중 스페이스x에서 내려오는 거대한 로켓을 강철 젓가락이 잡는 모습을 보며 충격을 먹었는데요. 이야 엄청난 세상이구나 하며 다시 공부하려 구글을 켰는데 open ai에서 한 프로젝트를 올렸더군요. swarm. multi agent 들 간 routines 과 handoffs 이라는 것을 사용해, 협력적인 시스템을 이룰 수 있게 하는 framework입니다. 이번 시간에는 swarm에 대해 '간단하게' 알아보겠습니다. 

[About openai swarm](https://github.com/openai/swarm)

->git 에서도 언급했듯이 이 framework은 아직 실험적 교육적 목적을 위한 초기 단계이지만, swarm을 이용한다면 이젠 혼자서도 회사 하나를 경영할 수 있는 수준에 이룰 수 있을 것입니다. 

이 이미지들은 swarm examples 중 하나인 airline 의 실행 결과입니다. 
![start](/assets/img/post_swarm/starting_conversation%20.png)<br>

![tri](/assets/img/post_swarm/triage_agent.png)<br>

![change_flight](/assets/img/post_swarm/flight_agent_answered.png) 

### Routines & Handoffs  
여기서의 핵심 개념인 routine과 handoff에 대해 
알아보겠습니다. 

## Routines 
루틴은 agent의 지시사항들에 대해 저장해놓은 것입니다. 기본적으로 gpt 모델, LLM 모델들을 사용하기에 지시사항들에 대해 유연하지만 간결함과 견고함을 바탕으로 작동합니다. 

## Handoffs 
swarm의 핵심이라고 생각되는 부분입니다. 위의 이미지들에서도 확인할 수 있듯이 triage agent가 본인이 할 수 없는 task를 비행 일정을 조정하는 agent에게 건네주는(handoff) 것을 볼 수 있습니다. 이것이 의미하는 바는 다양한 역할을 가진 agent들이 본인이 잘할 수 있는 task만 처리하고 나머지는 그걸 잘할 수 있는 agents에게 넘겨 협력적인 시스템을 이루어 과제를 처리한다는 것입니다. 


### 함 해봅시다 
python이 최소 3.10 이상이어야 합니다. 
``` pip install git+https://github.com/openai/swarm.git ```
or 
```pip install git+ssh://git@github.com/openai/swarm.git```
이게 안된다면 pip3 or pip3.10 으로 하시면 됩니다. 

examples을 실행시켜 보고 싶다면 
1. git clone 을 하세요 
2. llm 모델, 특히 gpt4o 와 같은 모델을 이용하므로 유료 플랜이 필요합니다. 이뿐만 아니라 api key을 사용해야 하는데 gpt4o을 이용하기 위해서는 tier1 이상의 결제가 필요합니다. 이 부분은 
[check your limits](https://platform.openai.com/settings/proj_cuOFLTGI9GAEaUrV2hpCt6n4/limits)
에서 확인하실 수 있습니다. tier1 이상의 계정과 api key까지 받으셨다면 준비 할 완료 입니다. 
3. api key를 저장할 .env 파일을 생성합니다. 
4. ```pip install python-dotenv```
 ```pip install openai```  를 설치합니다. 
5. 그리고 해당 examples 의 main.py으로 이동하여, 
api key를 인식시켜줘야 합니다. 저 같은 경우에는 
```
import os
from dotenv import load_dotenv
from configs.agents import *
from swarm.repl import run_demo_loop
from openai import OpenAI

# .env 파일에서 환경 변수 로드
load_dotenv()

# 환경 변수에서 API 키 가져오기
api_key = os.getenv("OPENAI_API_KEY")

if not api_key:
    raise ValueError("OPENAI_API_KEY not found in environment variables")

# OpenAI 클라이언트 초기화
client = OpenAI(api_key=api_key)


# OpenAI 모듈에도 API 키 설정
import openai
openai.api_key = api_key
```

이 부분을 넣었습니다. 이렇게 .env 을 만들어 나중에 gitignore 파일에 시켜 보안을 유지하는 것을 추천드립니다. 이 방법 말고는 api key를 하드코딩하여 main.py에 직접 작성하는 방법도 있으나, 이 방법은 git 잘못 올리거나 하면 굉장히 위험해지기에 추천하지는 않습니다. 


### Conclusion 
시험이 끝난다면 본격적으로 swarm을 활용하는 프로젝트를 만들어봐야겠습니다. 
감사합니다. 
