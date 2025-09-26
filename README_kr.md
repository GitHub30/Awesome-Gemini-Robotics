<div align="center">
<img width="3334" height="992" alt="image" src="https://github.com/user-attachments/assets/32195746-89c3-4e33-a93d-00a7f0642f79" />

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![English](https://img.shields.io/badge/English-Click_to_View-yellow)](README.md)
[![Chinese](https://img.shields.io/badge/汉语-撳一下就可以睇到-orange)](README_zh.md)
[![Japanese](https://img.shields.io/badge/日本語-クリックして表示-green)](README_ja.md)
[![Korean](https://img.shields.io/badge/한국어-눌러서_보기-blue)](README_kr.md)
[![Vietnamese](https://img.shields.io/badge/tiếng_Việt-Nhấp_để_xem-blueviolet)](README_vn.md)
</div>

# Awesome Gemini Robotics

**Gemini Robotics-ER 1.5** (체화된 추론, VLM) 예제와 프롬프트의 큐레이션된, 커뮤니티 유지 갤러리로, 여러분의 프로젝트에 복사하여 붙여넣을 수 있습니다. 각 카드에는 "Awesome Nano Banana Images" 스타일을 따라 **대표 이미지**와 **바로 사용 가능한 프롬프트**(종종 JSON 친화적)가 포함되어 있습니다.
멋진 것을 만드셨다면, PR을 보내주세요!

> **Gemini Robotics-ER 1.5란 무엇인가요?**
> 이것은 로보틱스를 위한 구글의 최첨단 체화된 추론 모델입니다. ER 1.5는 **공간 이해(2D 포인트, 박스, 궤적)**, **장기 작업 계획**, **비디오에 대한 시간적 추론**, **도구 사용(예: 구글 검색)**, **성공/진행률 추정**에 뛰어납니다. 구글 AI 스튜디오 / Gemini API를 통해 직접 호출하고 자신만의 로봇 API 또는 VLA를 오케스트레이션할 수 있습니다. ([Google Developers Blog])

---

## 목차

* [빠른 시작](#빠른-시작)
* [사용 사례 (10+)](#사용-사례)
* [기여하기](#기여하기)
* [라이선스 및 이미지 저작권](#라이선스-및-이미지-저작권)

---

## 빠른 시작

> Gemini API를 사용한 최소한의 파이썬 스케치 (모델 이름은 초기 액세스 시 `gemini-robotics-er-1.5-preview`로 나타날 수 있음):

```python
from google import genai
from google.genai import types

client = genai.Client()
MODEL_ID = "gemini-robotics-er-1.5-preview"

prompt = """
이미지에서 최대 10개의 항목을 가리킵니다.
y/x가 0-1000으로 정규화된 [{"point":[y,x],"label":"<name>"}]을 반환합니다.
"""

img_bytes = open("scene.jpg","rb").read()
res = client.models.generate_content(
    model=MODEL_ID,
    contents=[types.Part.from_bytes(data=img_bytes, mime_type="image/jpeg"), prompt],
    config=types.GenerateContentConfig(
        temperature=0.5,
        thinking_config=types.ThinkingConfig(thinking_budget=0)  # 필요한 경우 조정
    )
)
print(res.text)
```

이것은 구글의 공식 로보틱스 개요(포인트, 궤적, 오케스트레이션, 비디오 등)를 반영합니다. 더 많은 예제(객체 추적, 점심 싸기, 커스텀 로봇 API 호출)는 문서를 참조하세요. ([Google AI for Developers])

---

## 사용 사례

> ✅ = 구글 문서/블로그의 공개 데모 이미지 포함 • 🧩 = 자신만의 이미지 가져오기 (플레이스홀더 경로)

### 1) 정의되지 않은 객체 가리키기

<img width="801" height="598" alt="image" src="https://github.com/user-attachments/assets/35881c79-922e-43dd-9d1c-b22e4f66715d" />

**프롬프트**

```
이미지에서 최대 10개의 항목을 가리킵니다. 반환되는 레이블은 감지된 객체를 식별하는 이름이어야 합니다.
답변은 json 형식: [{"point": <point>, "label": <label1>}, ...]을 따라야 합니다. 포인트는 0-1000으로 정규화된 [y, x] 형식입니다.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 2) 정의된 객체 가리키기

<img width="800" height="601" alt="image" src="https://github.com/user-attachments/assets/8bf517fa-a094-40c9-be65-6c7bedfde295" />

**프롬프트**

```
다음 객체와 일치하는 모든 포인트를 가져옵니다: 빵, 스타프루트, 바나나. 반환되는 레이블은 감지된 객체를 식별하는 이름이어야 합니다.
답변은 json 형식: [{{"point": <point>, "label": <label1>}}, ...]을 따라야 합니다. 포인트는 0-1000으로 정규화된 [y, x] 형식입니다.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 3) 더 추상적인 설명(예: "과일")을 기반으로 객체의 모든 인스턴스 가리키기

<img width="798" height="597" alt="image" src="https://github.com/user-attachments/assets/974995c1-9a92-4c04-9e89-3bcbefdd22d8" />

**프롬프트**

```
과일에 대한 모든 포인트를 가져옵니다. 반환되는 레이블은 감지된 객체를 식별하는 이름이어야 합니다.
답변은 json 형식: [{"point": <point>, "label": <label1>}, ...]을 따라야 합니다. 포인트는 0-1000으로 정규화된 [y, x] 형식입니다.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 4) 객체의 모든 인스턴스 가리키기

<img width="799" height="450" alt="image" src="https://github.com/user-attachments/assets/93382594-8156-43ff-9864-aa627346b2ae" />

**프롬프트**

```
게임 보드 슬롯과 일치하는 모든 포인트를 가져옵니다. 반환되는 레이블은 감지된 객체를 식별하는 이름이어야 합니다.
답변은 json 형식: [{"point": <point>, "label": <label1>}, ...]을 따라야 합니다. 포인트는 0-1000으로 정규화된 [y, x] 형식입니다.
```

```
X 게임 조각과 일치하는 모든 포인트를 가져옵니다. 반환되는 레이블은 감지된 객체를 식별하는 이름이어야 합니다.
답변은 json 형식: [{"point": <point>, "label": <label1>}, ...]을 따라야 합니다. 포인트는 0-1000으로 정규화된 [y, x] 형식입니다.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 5) 객체의 특정 부분을 순차적으로 가리키기

<img width="795" height="592" alt="image" src="https://github.com/user-attachments/assets/68c6bd2f-a064-43e4-bee3-a377a33d73c7" />

**프롬프트**

```
queries = [
    ("종이 봉투", "손잡이"),
    ("바나나", "줄기"),
    ("바나나", "중앙"),
    ("스타프루트", "중앙"),
    ("라임", "중앙"),
    ("밝은 파란색 그릇", "가장자리"),
    ("어두운 파란색 그릇", "가장자리"),
    ("계량컵", "가장자리"),
    ("계량컵", "손잡이"),
    ("그릇", "토마토"),
]

이미지에서 $object의 $part를 가리킵니다. 답변을 "point"와 "label" 키를 가진 딕셔너리의 json 리스트로 반환합니다. 이 요청에 대해 하나의 포인트만 반환합니다.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 6) 가리키며 세기

<img width="793" height="820" alt="image" src="https://github.com/user-attachments/assets/2592ca4e-c1a9-4510-a251-c63dbbeaa52d" />

**프롬프트**

```
상자 안의 각 와셔를 가리킵니다.
답변을 형식: [{"point": <point>, "label": <label1>}, ...]으로 반환합니다. 포인트는 0-1000으로 정규화된 [y, x] 형식입니다.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 7) GIF에서 정의된 객체 가리키기

![다운로드 (1)](https://github.com/user-attachments/assets/635a748f-eae9-44c3-88b8-31627eec0ed8)

**프롬프트**

```
queries = [
    "펜 (책상 위)",
    "펜 (로봇 손 안)",
    "노트북 (열림)",
    "노트북 (닫힘)",
]

제공된 이미지에서 다음 객체를 가리킵니다: {', '.join(queries)}.
답변은 json 형식: [{{"point": <point>, "label": <label1>}}, ...]을 따라야 합니다.
포인트는 0-1000으로 정규화된 [y, x] 형식입니다.
객체를 찾지 못한 경우 빈 JSON 리스트 []를 반환합니다.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 8) 2D 경계 상자

<img width="794" height="597" alt="image" src="https://github.com/user-attachments/assets/afaa8594-e682-4aaa-aa4c-62c468a66e49" />

**프롬프트**

```
경계 상자를 레이블이 있는 JSON 배열로 반환합니다. 마스크나 코드 펜싱은 절대 반환하지 마십시오. 25개 객체로 제한합니다. 테이블에서 식별할 수 있는 만큼 많은 객체를 포함하십시오.
객체가 여러 번 나타나는 경우 고유한 특성(색상, 크기, 위치, 고유 특성 등)에 따라 이름을 지정하십시오.
형식은 다음과 같아야 합니다: [{"box_2d": [ymin, xmin, ymax, xmax], "label": <객체 레이블>}] 0-1000으로 정규화. box_2d의 값은 정수여야 합니다.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 9) 간단한 궤적 계획

<img width="794" height="444" alt="image" src="https://github.com/user-attachments/assets/5c2f94dc-9b08-4e2b-b1d3-58ca55c6fb51" />

**프롬프트**

```
빨간 펜에 점을 하나 찍고, 빨간 펜을 왼쪽 정리함 상단으로 옮기는 궤적에 대해 15개의 점을 찍습니다.
점들은 궤적 순서에 따라 '0'(왼손의 시작점)부터 <n>(최종점)까지 레이블을 지정해야 합니다.
답변은 json 형식: [{"point": <point>, "label": <label1>}, ...]을 따라야 합니다.
포인트는 0-1000으로 정규화된 [y, x] 형식입니다.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 10) 입자 청소를 위한 경로

<img width="792" height="592" alt="image" src="https://github.com/user-attachments/assets/855c0a41-29f7-4d3b-a774-c22e4d726ac4" />

**프롬프트**

```
파란색 브러시와 입자 영역을 덮는 10개의 점 목록을 가리킵니다.
부드러운 궤적을 만들기 위해 점들이 입자 위에 고르게 분포되도록 하십시오.
접시를 청소하는 궤적에서 접근해야 할 순서에 따라 1부터 10까지 점에 레이블을 지정하십시오.
움직임은 브러시에서 시작해야 합니다.
답변은 json 형식: [{"point": <point>, "label": <label1>}, ...]을 따라야 합니다.
포인트는 0-1000으로 정규화된 [y, x] 형식입니다.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 11) 장애물 회피 궤적 계획

<img width="801" height="743" alt="image" src="https://github.com/user-attachments/assets/53cfab6e-db07-4eb7-b325-27d60da55eee" />

**프롬프트**

```
현재 시점 원점과 왼쪽 뒤에 있는 녹색 오토만 사이의 바닥에서 가장 직접적인 충돌 없는 10개의 점 궤적을 찾습니다. 점들은 바닥의 다른 모든 장애물을 피해야 합니다.
답변은 json 형식: [{"point": <point>, "label": <label1>}, ...]을 따라야 합니다.
포인트는 0-1000으로 정규화된 [y, x] 형식입니다.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 12) 노트북을 놓을 공간을 만들기 위해 제거할 항목

<img width="795" height="598" alt="image" src="https://github.com/user-attachments/assets/70a17400-d409-4ec9-8c23-4a2595f528ec" />

**프롬프트**

```
노트북을 놓을 공간을 만들기 위해 제거해야 할 객체를 가리킵니다.
답변은 json 형식: [{"point": <point>, "label": <label1>}, ...]을 따라야 합니다. 포인트는 0-1000으로 정규화된 [y, x] 형식입니다.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 13) 오케스트레이션: 점심 싸기

<img width="1207" height="855" alt="image" src="https://github.com/user-attachments/assets/aab2f569-99fe-4d7e-ae34-159655729a59" />

**프롬프트**

```
도시락과 도시락 가방을 싸는 방법을 설명합니다. 참조하는 각 객체를 가리킵니다. 각 포인트는 [{"point": [y, x], "label": }] 형식이어야 하며, 좌표는 0-1000 사이로 정규화됩니다.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 14) 비어있는 전기 소켓

<img width="792" height="938" alt="image" src="https://github.com/user-attachments/assets/469d73df-3b01-48d6-b1f5-e39b62aa5133" />

**프롬프트**

```
가려지지 않은 빈 소켓을 가리킵니다.
답변은 json 형식: [{"point": <point>, "label": <label1>}, ...]을 따라야 합니다. 포인트는 0-1000으로 정규화된 [y, x] 형식입니다.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 15) 항목 리프트 제한 (3LB 제한)

<img width="792" height="954" alt="image" src="https://github.com/user-attachments/assets/a45216e1-cac7-4217-b51e-98d0d23e8754" />

**프롬프트**

```
저는 3LBs의 탑재량을 가진 로봇입니다. 이미지에서 제가 물리적으로 집을 수 있는 모든 객체를 가리킵니다.
답변은 json 형식: [{"point": <point>, "label": <label1>}, ...]을 따라야 합니다. 포인트는 0-1000으로 정규화된 [y, x] 형식입니다.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 16) 비디오 분석

https://github.com/user-attachments/assets/cc2b3c62-2ce6-4c7b-bcf8-968f000e37f5

**프롬프트**

```
작업을 완료하는 각 단계를 자세히 설명합니다. 타임스탬프별로 나누어 "start_timestamp", "end_timestamp" 및 "description" 키를 사용하여 json 형식으로 출력합니다.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 17) 비디오 분석: 시간 범위

```py
conversation_history = [
    {
        'role': 'user',
        'parts': [{'text': prompt}]
    },
    {
        'role': 'model',
        'parts': [{'text': response.text}]
    }
]

chat = client.chats.create(
    model=MODEL_ID,
    history=conversation_history
)

prompt = """
15초에서 22초까지 확대하여 동일한 형식으로 초당 분석을 제공합니다.
"""

start_time = time.time()

response = chat.send_message(
    [prompt, myfile]
)

end_time = time.time()
elapsed_time = end_time - start_time
print(f"\n총 처리 시간: {elapsed_time:.4f} 초")

print(response.text)
```

총 처리 시간: 11.4140 초
```json
[
  {
    "start_timestamp": "00:15",
    "end_timestamp": "00:16",
    "description": "왼쪽 로봇 팔이 테이블 위 위치에서 파란색 펜을 향해 움직이기 시작합니다."
  },
  {
    "start_timestamp": "00:16",
    "end_timestamp": "00:18",
    "description": "왼쪽 로봇 팔이 테이블 위의 파란색 펜 바로 위에 그리퍼를 위치시킵니다."
  },
  {
    "start_timestamp": "00:18",
    "end_timestamp": "00:19",
    "description": "왼쪽 로봇 팔이 그리퍼를 내리고 파란색 펜을 잡습니다."
  },
  {
    "start_timestamp": "00:19",
    "end_timestamp": "00:20",
    "description": "왼쪽 로봇 팔이 테이블 표면에서 파란색 펜을 들어 올립니다."
  },
  {
    "start_timestamp": "00:20",
    "end_timestamp": "00:21",
    "description": "왼쪽 로봇 팔이 파란색 펜을 테이블 중앙으로 이동시켜 검은색 메쉬 펜 홀더 위에 위치시킵니다."
  },
  {
    "start_timestamp": "00:21",
    "end_timestamp": "00:22",
    "description": "왼쪽 로봇 팔이 파란색 펜을 놓아 검은색 메쉬 펜 홀더에 떨어뜨립니다."
  }
]
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 18) 네 번째 선반 행 찾기

<img width="794" height="922" alt="image" src="https://github.com/user-attachments/assets/f7d26839-67a8-4215-b23d-e3eeb89ff11e" />

**프롬프트**

```
네 번째 선반 행의 모든 칸을 강조하는 레이블이 있는 JSON 배열로 경계 상자를 반환합니다.
형식은 다음과 같아야 합니다: [{"box_2d": [ymin, xmin, ymax, xmax], "label": <객체 레이블>}] 0-1000으로 정규화. box_2d의 값은 정수여야 합니다.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 19) 특정 항목이 있는 선반 찾기

<img width="794" height="942" alt="image" src="https://github.com/user-attachments/assets/95baca11-e7aa-485c-a927-0e392d9f6390" />

**프롬프트**

```
"코를 풀어야 해요."
도움이 될 수 있는 칸을 찾으세요.
형식은 다음과 같아야 합니다: [{"box_2d": [ymin, xmin, ymax, xmax], "label": <객체 레이블>}] 0-1000으로 정규화. box_2d의 값은 정수여야 합니다.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 20) 생각하며 항목 세기

<img width="960" height="600" alt="image" src="https://github.com/user-attachments/assets/d902b60a-006e-406a-8641-611240dd9f87" />

**출력**

```
이미지 전경에 있는 쇼핑 카트 바구니를 육안으로 검사한 결과, 안에는 **8개**의 항목이 있습니다.

개수에 대한 추론은 다음과 같습니다:

1.  **쌓인 상자:** 바구니 왼쪽에 여러 상자가 쌓여 있습니다. 이 더미에서 최소 5개의 상자를 명확하게 식별할 수 있습니다:
    *   왼쪽 하단에 파란색 Barilla 파스타 상자.
    *   Barilla 상자 옆에 노란색 Wheat Thins 상자.
    *   이 두 상자 위에 부분적으로 보이는 최소 3개 이상의 상자.
2.  **캔:** 바구니 오른쪽 하단에 은색 캔 2개가 보입니다.
3.  **패킷:** 상자와 캔 사이 바구니 중앙에 작은 파란색 패킷이 하나 있습니다.
```

**프롬프트**

```
카트 바구니 안에 항목이 몇 개 있나요? 추론 과정을 공유해 주세요.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 21) 특정 항목이 있는 선반 찾기

| ![initial_state_1](https://storage.googleapis.com/generativeai-downloads/images/robotics/er-1-5-example-colab/initial_state_1.png) | ![initial_state_2](https://storage.googleapis.com/generativeai-downloads/images/robotics/er-1-5-example-colab/initial_state_2.png) | ![initial_state_3](https://storage.googleapis.com/generativeai-downloads/images/robotics/er-1-5-example-colab/initial_state_3.png) | ![initial_state_4](https://storage.googleapis.com/generativeai-downloads/images/robotics/er-1-5-example-colab/initial_state_4.png) |
|---|---|---|---|
| ![current_state_1](https://storage.googleapis.com/generativeai-downloads/images/robotics/er-1-5-example-colab/current_state_1.png) | ![current_state_2](https://storage.googleapis.com/generativeai-downloads/images/robotics/er-1-5-example-colab/current_state_2.png) | ![current_state_3](https://storage.googleapis.com/generativeai-downloads/images/robotics/er-1-5-example-colab/current_state_3.png) | ![current_state_4](https://storage.googleapis.com/generativeai-downloads/images/robotics/er-1-5-example-colab/current_state_4.png) |


**출력**

성공? 아니요

**프롬프트**

```
이 작업에서는 로봇이나 사람이 망고를 갈색 용기에 넣는 작업을 수행하려고 하는 것을 보게 됩니다.
동일한 장면의 여러 카메라 뷰를 볼 수 있습니다. 일부 카메라는 정적이며
장면 외부에 장착되어 있고 일부 카메라는 로봇 팔에 장착되어 있습니다.
따라서 에피소드 중에 움직입니다. 처음 4개의 이미지는
에피소드 시작(얼마 전)부터 여러 카메라 뷰를 보여줍니다. 마지막 4개의 이미지는
에피소드의 현재 순간(현재 상태)부터 여러 카메라 뷰를 보여줍니다.
이 이미지들을 보고 에피소드 시작과 현재 상태를 비교했을 때
로봇이 "망고를 갈색 용기에 넣기" 작업을 성공적으로 수행했습니까?
(1) 예 또는 (2) 아니요로만 답하십시오. 질문에 가장 잘 답하는 숫자 (1) 또는 (2)를 반환하십시오.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 22) 더 나은 판독을 위해 이미지 섹션 확대

<img width="794" height="959" alt="image" src="https://github.com/user-attachments/assets/338a9381-8f71-4f61-a378-a57a9bc64f75" />

**프롬프트**

```
공기질 측정값은 얼마입니까? 코드 실행 기능을 사용하여 이미지를 확대하여 자세히 살펴보세요.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 23) 로봇 그리퍼와 항목 분할

<img width="796" height="456" alt="image" src="https://github.com/user-attachments/assets/09403f45-f6e7-4fd1-8390-4614e3481e3a" />

**프롬프트**

```
이 이미지에서 다음 객체에 대한 분할 마스크를 제공하십시오: 망고, 왼쪽 로봇 그리퍼 손가락, 오른쪽 로봇 그리퍼 손가락.
답변은 JSON 형식을 따라야 합니다:
[
  {
    "box_2d": [ymin, xmin, ymax, xmax],
    "label": "<객체 레이블>",
    "mask": "data:image/png;base64,<base64 인코딩된 PNG 마스크>"
  },
  ...
]
box_2d 좌표는 0-1000으로 정규화되어야 하며 정수여야 합니다.
마스크는 0이 아닌 픽셀이 마스크를 나타내는 base64 인코딩된 PNG 이미지여야 합니다.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 24) 관련 객체 찾기

<img width="793" height="504" alt="image" src="https://github.com/user-attachments/assets/69d4df26-a15d-4fcb-8ad7-d0b0cf9889b5" />

**프롬프트**

```
파란색 블록과 주황색 그릇을 찾아 가리킵니다. 반환되는 레이블은
감지된 객체를 식별하는 이름이어야 합니다.
답변은 json 형식: [{"point": <point>, "label": <label1>}, ...]을 따라야 합니다.
포인트는 0-1000으로 정규화된 [y, x] 형식입니다.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 25) 주방 장면: **객체 가리키고 이름 말하기** (2D 포인트) ✅

![주방 가리키기](https://storage.googleapis.com/gweb-developer-goog-blog-assets/images/unnamed-2_2.original.png)

**프롬프트**

```
이미지에서 다음 항목을 가리킵니다: 주방 세제, 식기 건조대, 수도꼭지, 밥솥, 유니콘.
포인트는 0-1000으로 정규화된 [y, x] 형식입니다.
실제로 이미지에 있는 객체만 포함하십시오.
반환: [{"point":[y,x],"label":"<name>"}]
```

ER 1.5는 정확하고 의미론적으로 기반한 포인트를 반환합니다. 이를 깊이/포즈 스택과 결합하여 모션 계획을 세웁니다. ([Google Developers Blog])

---

### 26) 비디오에 대한 시간적 추론: **언제 무슨 일이 일어났는가?** 🧩




https://github.com/user-attachments/assets/5c55cbea-6946-4d8f-8e33-a17c4f0d6dd7




**프롬프트**

```
짧은 로봇 조작 비디오가 주어집니다.
정확한 타임스탬프와 함께 각 단계를 JSON으로 설명하십시오:
[{"start_timestamp":"MM:SS","end_timestamp":"MM:SS","description":"..."}]
```

ER 1.5는 **순서, 인과 관계, 간격**(예: "녹색 마커를 트레이에 넣은 다음 파란색/빨간색 펜을 컵에 넣기")에 대해 추론할 수 있으며 하위 간격으로 확대할 수도 있습니다. 이를 사용하여 실행을 확인하고 에피소드에 주석을 답니다. ([Google Developers Blog])

---

### 27) 커피 메이커: **닫는 궤적 계획** (다중 포인트 경로) ✅

![커피 뚜껑 궤적](https://storage.googleapis.com/gweb-developer-goog-blog-assets/images/image1_PCWZQMD.original.png)

**프롬프트**

```
이제 커피 메이커를 닫아야 합니다.
손잡이가 닫히기 위해 어떻게 움직여야 하는지를 나타내는 8개 포인트의 궤적을 그리십시오.
손잡이에서 시작하십시오. 포인트는 [Y,X]이며 0-1000으로 정규화됩니다.
반환:
[{"point":[Y,X],"label":"p0"}, {"point":[Y,X],"label":"p1"}, ...]
```

모션 플래너(스무딩, 충돌 확인)를 시드하는 데 출력을 사용합니다. ([Google Developers Blog])

---

### 28) 커피 정리: **머그를 어디에 두어야 할까요?** (어포던스 가리키기) ✅

![머그 치우기](https://storage.googleapis.com/gweb-developer-goog-blog-assets/images/image12_O0rW8PY.original.png)

**프롬프트**

```
커피를 다 마셨습니다. 이제 정리하기 위해 머그를 어디에 두어야 할까요?
y/x가 0-1000으로 정규화된 [{"point":[y,x],"label":"<best place>"}]을 반환합니다.
```

작업 완료 후 **어포던스를 인식하는 배치**에 적합합니다. ([Google Developers Blog])

---

### 29) **지역 규칙에 따른 쓰레기 분리수거** (도구 사용 + 공간적 기반) 🧩

https://github.com/user-attachments/assets/503b20d6-1995-4e71-9164-9a79d6bf6a4c

**프롬프트**

```
Google 검색 도구를 사용하여 우리 도시의 최신 재활용/퇴비 규칙을 가져옵니다.
그런 다음 이 이미지를 기반으로 보이는 각 항목을 올바른 통에 분류하는 방법을 설명하십시오.
0-1000으로 정규화된 [{"point":[y,x],"label":"<bin>"}]을 사용하여 참조된 각 항목을 가리킵니다.
VLA 컨트롤러에 전달할 수 있는 단계별 계획을 반환하십시오.
```

ER 1.5는 **도구를 호출**(예: Google 검색)하고 "공간적으로 기반한" 계획을 위해 **텍스트 + 포인트가 인터리브된** 출력을 할 수 있습니다. ([Google Developers Blog])

---

### 30) **참고 사진으로 내 책상 재정리하기** (장기 계획) 🧩

**프롬프트**

```
(A) 현재 내 책상 사진과 (B) 참고 사진이 주어졌을 때,
단계별 재정리 계획을 만드십시오.
각 단계에는 0-1000으로 정규화된 [{"box_2d":[ymin,xmin,ymax,xmax],"label":"<item>"}]으로 목표 배치를 포함하고
1-2 문장의 근거를 포함하십시오.
```

여러 단계에 걸쳐 **공간적 기반**을 갖춘 **에이전트 계획**을 보여줍니다. ([Google Developers Blog])

---

### 31) **노트북을 놓을 공간 만들기** (이동할 것 선택) 🧩

![공간 만들기](https://ai.google.dev/gemini-api/docs/images/robotics/spatial-reasoning.png)

**프롬프트**

```
노트북을 놓을 공간을 만들기 위해 제거해야 할 단일 객체를 가리킵니다.
0-1000으로 정규화된 [{"point":[y,x],"label":"<object to move>"}]을 반환합니다.
```

문서의 전형적인 **오케스트레이션** 예제—제약 조건에 대해 추론하고 최소한의 변경을 선택합니다. ([Google AI for Developers])

---

### 32) **공간적으로 기반한 지침으로 점심 싸기** ✅

![점심 싸기](https://ai.google.dev/static/gemini-api/docs/images/robotics/packing-lunch.png)

**프롬프트**

```
이 이미지에서 도시락과 도시락 가방을 싸는 방법을 설명하십시오.
참조하는 각 객체를 가리킵니다:
0-1000으로 정규화된 [{"point":[y,x],"label":"<name>"}]
번호가 매겨진 단계별 계획을 반환하십시오.
```

렌더링하거나 픽 앤 플레이스 호출로 변환할 수 있는 **다단계 계획**과 포인트를 출력합니다. ([Google AI for Developers])

---

### 33) **로봇 API를 통한 픽 앤 플레이스** (함수 호출) 🧩

![블록과 그릇](https://ai.google.dev/gemini-api/docs/images/robotics/robot-api-example.png)

**프롬프트**

```
먼저 다음을 사용하여 파란색 블록과 주황색 그릇을 찾습니다:
0-1000으로 정규화된 [{"point":[y,x],"label":"<name>"}]
그런 다음 다음 함수만 사용하여:
  def move(x, y, high: bool)
  def setGripperState(opened: bool)
  def returnToOrigin()
블록을 집어 그릇에 놓는 정확한 호출 순서를 생성하십시오.
각 호출을 간략하게 설명하십시오.
```

문서에서는 이 패턴을 엔드 투 엔드로 보여줍니다(감지 → 계획 → API 호출). ([Google AI for Developers])

---

### 34) **빨간 펜을 정리함으로 옮기기** (궤적 계획) 🧩

![펜 궤적](https://ai.google.dev/gemini-api/docs/images/robotics/trajectories.png)

**프롬프트**

```
빨간 펜에 점을 하나 찍은 다음, 정리함으로 가는 궤적에 15개의 점을 찍습니다.
"0"..."15" 순서로 점에 레이블을 지정하십시오. [{"point":[y,x],"label":"<step>"}]을 반환합니다.
```

**궤적을 통한 학습** 및 다운스트림 스플라인 피팅/시간 매개변수화에 적합합니다. ([Google AI for Developers])

---

### 35) **색상별 빨래 분류** (다단계 사고) 🧩

[![](https://i.imgur.com/2fqyZh6.png)](https://www.youtube.com/watch?v=eDyXEh8XqjM&t=34s)


**프롬프트**

```
빨래를 흰색과 비흰색 두 바구니로 색상별로 분류하십시오.
짧은 자연어 계획과 각 의류에 대해 정규화된 2D 좌표를 가진
{"point":[y,x],"label":"white|non-white"}를 반환하십시오.
```

공식 블로그에서는 로보틱스 1.5/ER 1.5가 **색상별 빨래 분류**와 같은 집안일을 위해 작업/하위 작업/미세 행동 수준에서 어떻게 생각하는지 설명합니다. ([blog.google])

---

### 36) **탑재량 인식 픽** (안전 및 제약 조건) 🧩


**프롬프트**

```
1.0kg 탑재량 제한과 25cm 도달 반경을 조건으로
보이는 가장 무거운 항목에 대한 픽 앤 플레이스를 계획하십시오.
제약 조건 확인을 설명하고 안전하지 않은 경우 거부하십시오.
[{"point":[y,x],"label":"grasp"}]과 "reason"을 반환하십시오.
```

ER 1.5는 향상된 의미론적 안전과 물리적 제약 조건에 대한 인식을 추가합니다. 하드웨어 수준의 안전은 여전히 구현해야 합니다. ([Google Developers Blog])

---

### 37) **인간-로봇 자연어**: "테이블 정리해" 🧩

![테이블 정리](https://storage.googleapis.com/gweb-developer-goog-blog-assets/images/image12_O0rW8PY.original.png)

**프롬프트**

```
테이블 장면 이미지가 주어지면 번호가 매겨진 정리 계획을 생성하십시오.
각 단계에 대해 목표 배치 지점 또는 2D 상자와 성공 기준에 대한 참고 사항을 포함하십시오.
형식:
{"step":n,"action":"...","target":{"point":[y,x] | "box_2d":[ymin,xmin,ymax,xmax]},"why":"..."}
```

ER 1.5는 모호한 명령을 실행 가능한 하위 목표로 분해하고 전문화된 잡기/VLA 기술을 호출하여 행동할 수 있습니다. ([Google Developers Blog])

---

### 38) **교차 구현 계획** (로봇에 구애받지 않는 단계) 🧩

[![구현](https://github.com/user-attachments/assets/919be470-810b-4974-8fe9-2784623aabd2)](https://www.youtube.com/watch?v=9FV5ZYytkOQ)

**프롬프트**

```
이 워크스테이션 장면에서,
환경/어포던스 용어로만 표현된 로봇에 구애받지 않는 계획을 생성하십시오(관절 공간 없음).
ALOHA 2, Apptronik Apollo 또는 Franka 팔에서 실행될 것이라고 가정하십시오.
성공 확인 및 대체 옵션이 포함된 단계 목록을 반환하십시오.
```

딥마인드는 기술 이전을 가속화하기 위해 구현 간의 일반화(예: ALOHA-2 → Apollo/Franka)를 강조합니다. ([blog.google])

---

## 팁 및 패턴

* 점에는 **0–1000으로 정규화된 [y,x]**를, 상자에는 `[ymin,xmin,ymax,xmax]`를 선호합니다. 이렇게 하면 프롬프트가 모델 친화적이고 구현에 구애받지 않게 됩니다. ([Google Developers Blog])
* 작업 복잡성에 따라 **사고 예산**(지연 시간/정확도 트레이드오프)을 조정합니다. ([Google Developers Blog])
* 컨트롤러가 실행할 수 있는 "공간적으로 기반한" 계획을 생성하기 위해 **텍스트 + 점/상자/궤적**을 인터리브합니다. ([Google Developers Blog])
* 계획을 지역 규칙(재활용, 주방 정책 등)에 기반하기 위해 **도구 호출**(예: 검색)을 사용합니다. ([Google Developers Blog])

---

## 기여하기

* `cases/<short-name>/` 아래에 새 폴더를 추가하고 다음을 포함합니다:

  * `README.md` (1–2 문장 + 프롬프트)
  * `image.jpg/png` (또는 링크)
* 프롬프트를 **복사-실행 가능**하고 **JSON 친화적**으로 유지합니다.
* 출처를 인용합니다(기본 문서/블로그/비디오 선호).

---

## 라이선스 및 이미지 저작권

* 텍스트: MIT (이 저장소).
* ✅로 표시된 이미지는 구글의 공개 문서/블로그에서 가져온 것이며 여기서는 **데모 참조**로만 사용됩니다. 재배포하기 전에 출처 라이선스를 확인하십시오. 🧩 플레이스홀더를 자신의 이미지로 바꾸십시오.

**주요 출처**

* Google Developers Blog — *Building the Next Generation of Physical Agents with Gemini Robotics-ER 1.5* (공식 프롬프트, 이미지, 기능). ([Google Developers Blog])
* Google AI for Developers — *Gemini Robotics-ER 1.5 (로보틱스 개요)* (포인트, 궤적, 오케스트레이션, 코드). ([Google AI for Developers])
* Google / DeepMind 블로그 — ER 1.5 출시 컨텍스트, 체화된 추론, 교차 구현, JP 블로그에는 빨래 분류 예제 및 파트너 로봇 참조가 포함됩니다. ([Google DeepMind])

---

> 💡 이 파일을
> `https://github.com/GitHub30/Awesome-Gemini-Robotics/blob/main/README.md`에
> 넣고 `assets/` 아래에 자신의 스크린샷을 추가하여 🧩 플레이스홀더를 바꾸기 시작하십시오.

## 🙏 감사 인사

이 저장소의 다양한 사례는 AI 커뮤니티의 공유에 의존합니다. 모든 사례 기여자분들께 진심으로 감사의 말씀을 전합니다.

멋진 작업을 공유해 주신 다음 사용자분들께 감사드립니다. 프로필을 방문하여 더 많은 것을 배울 수도 있습니다:

- [@GoogleDeepMind](https://x.com/GoogleDeepMind)
- [@GeminiApp](https://x.com/GeminiApp)

*모든 사례가 원저자에게서 온 것임을 보장할 수는 없습니다. 이로 인해 불편을 드린 경우 언제든지 수정을 위해 저희에게 연락해 주십시오.*

저희가 수집한 사례는 모든 가능한 응용 시나리오를 다룰 수 없습니다. 다른 흥미로운 발견이 있으시면 🔍, 더 많은 창의성을 선보일 수 있도록 저희에게 연락해 주시기 바랍니다 📧!

[![Star History Chart](https://api.star-history.com/svg?repos=GitHub30/Awesome-Gemini-Robotics&type=Date)](https://www.star-history.com/#GitHub30/Awesome-Gemini-Robotics&Date)

: https://developers.googleblog.com/en/building-the-next-generation-of-physical-agents-with-gemini-robotics-er-15/ "Building the Next Generation of Physical Agents with Gemini Robotics-ER 1.5 - Google Developers Blog"
: https://ai.google.dev/gemini-api/docs/robotics-overview "Gemini Robotics-ER 1.5  |  Gemini API  |  Google AI for Developers"
: https://blog.google/intl/ja-jp/company-news/technology/gemini-robotics-15-ai/ "Gemini Robotics 1.5 を発表、AI エージェントを物理世界に"
: https://deepmind.google/discover/blog/gemini-robotics-15-brings-ai-agents-into-the-physical-world/ "Gemini Robotics 1.5 brings AI agents into the physical world - Google DeepMind"
