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

一个精选的、由社区维护的 **Gemini Robotics-ER 1.5**（具身推理，VLM）示例和提示的画廊，您可以将其复制粘贴到自己的项目中。每张卡片都包含一个**代表性图像**和一个**即用型提示**（通常是 JSON 友好的），遵循“Awesome Nano Banana Images”的风格。
如果您构建了很酷的东西，请发送一个 PR！

> **什么是 Gemini Robotics-ER 1.5？**
> 它是谷歌最先进的机器人具身推理模型。ER 1.5 在**空间理解（2D 点、框、轨迹）**、**长期任务规划**、**视频时间推理**、**工具使用（例如，谷歌搜索）**和**成功/进度估计**方面表现出色。您可以直接通过 Google AI Studio / Gemini API 调用它，并编排您自己的机器人 API 或 VLA。([谷歌开发者博客])

---

## 目录

* [快速入门](#快速入门)
* [用例 (10+)](#用例)
* [贡献](#贡献)
* [许可证和图像归属](#许可证和图像归属)

---

## 快速入门

> 使用 Gemini API 的最简 Python 代码（模型名称在早期访问中可能显示为 `gemini-robotics-er-1.5-preview`）：

```python
from google import genai
from google.genai import types

client = genai.Client()
MODEL_ID = "gemini-robotics-er-1.5-preview"

prompt = """
在图像中指出不超过 10 个项目。
返回 [{"point":[y,x],"label":"<name>"}]，其中 y/x 归一化到 0-1000。
"""

img_bytes = open("scene.jpg","rb").read()
res = client.models.generate_content(
    model=MODEL_ID,
    contents=[types.Part.from_bytes(data=img_bytes, mime_type="image/jpeg"), prompt],
    config=types.GenerateContentConfig(
        temperature=0.5,
        thinking_config=types.ThinkingConfig(thinking_budget=0)  # 如果需要，请调整
    )
)
print(res.text)
```

这反映了谷歌的官方机器人技术概述（点、轨迹、编排、视频等）。有关更多示例（对象跟踪、打包午餐、调用自定义机器人 API），请参阅文档。([Google AI for Developers])

---

## 用例

> ✅ = 包含来自谷歌文档/博客的公共演示图像 • 🧩 =自带图像（占位符路径）

### 1) 指向未定义的对象

<img width="801" height="598" alt="image" src="https://github.com/user-attachments/assets/35881c79-922e-43dd-9d1c-b22e4f66715d" />

**提示**

```
在图像中指出不超过 10 个项目。返回的标签应该是检测到的对象的识别名称。
答案应遵循 json 格式：[{"point": <point>, "label": <label1>}, ...]。点采用 [y, x] 格式，归一化到 0-1000。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 2) 指向已定义的对象

<img width="800" height="601" alt="image" src="https://github.com/user-attachments/assets/8bf517fa-a094-40c9-be65-6c7bedfde295" />

**提示**

```
获取与以下对象匹配的所有点：面包、杨桃、香蕉。返回的标签应该是检测到的对象的识别名称。
答案应遵循 json 格式：[{"point": <point>, "label": <label1>}, ...]。点采用 [y, x] 格式，归一化到 0-1000。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 3) 根据更抽象的描述（例如“水果”）指向对象的所有实例

<img width="798" height="597" alt="image" src="https://github.com/user-attachments/assets/974995c1-9a92-4c04-9e89-3bcbefdd22d8" />

**提示**

```
获取水果的所有点。返回的标签应该是检测到的对象的识别名称。
答案应遵循 json 格式：[{"point": <point>, "label": <label1>}, ...]。点采用 [y, x] 格式，归一化到 0-1000。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 4) 指向对象的所有实例

<img width="799" height="450" alt="image" src="https://github.com/user-attachments/assets/93382594-8156-43ff-9864-aa627346b2ae" />

**提示**

```
获取与游戏板槽匹配的所有点。返回的标签应该是检测到的对象的识别名称。
答案应遵循 json 格式：[{"point": <point>, "label": <label1>}, ...]。点采用 [y, x] 格式，归一化到 0-1000。
```

```
获取与 X 游戏棋子匹配的所有点。返回的标签应该是检测到的对象的识别名称。
答案应遵循 json 格式：[{"point": <point>, "label": <label1>}, ...]。点采用 [y, x] 格式，归一化到 0-1000。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 5) 依次指向对象的某些部分

<img width="795" height="592" alt="image" src="https://github.com/user-attachments/assets/68c6bd2f-a064-43e4-bee3-a377a33d73c7" />

**提示**

```
queries = [
    ("纸袋", "把手"),
    ("香蕉", "茎"),
    ("香蕉", "中心"),
    ("杨桃", "中心"),
    ("酸橙", "中心"),
    ("浅蓝色碗", "边缘"),
    ("深蓝色碗", "边缘"),
    ("量杯", "边缘"),
    ("量杯", "把手"),
    ("碗", "番茄"),
]

指向图像中 $object 的 $part。以 json 列表的形式返回答案，其中包含键“point”和“label”的字典。此请求仅返回一个点
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 6) 通过指向进行计数

<img width="793" height="820" alt="image" src="https://github.com/user-attachments/assets/2592ca4e-c1a9-4510-a251-c63dbbeaa52d" />

**提示**

```
指向盒子里的每个垫圈。
以以下格式返回答案：[{"point": <point>, "label": <label1>}, ...]。点采用 [y, x] 格式，归一化到 0-1000。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 7) 指向 GIF 中的已定义对象

![下载 (1)](https://github.com/user-attachments/assets/635a748f-eae9-44c3-88b8-31627eec0ed8)

**提示**

```
queries = [
    "笔（在桌子上）",
    "笔（在机器人手中）",
    "笔记本电脑（打开）",
    "笔记本电脑（关闭）",
]

指向提供的图像中的以下对象：{', '.join(queries)}。
答案应遵循 json 格式：[{"point": <point>, "label": <label1>}, ...]。
点采用 [y, x] 格式，归一化到 0-1000。
如果未找到对象，则返回一个空的 JSON 列表 []。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 8) 2D 边界框

<img width="794" height="597" alt="image" src="https://github.com/user-attachments/assets/afaa8594-e682-4aaa-aa4c-62c468a66e49" />


**提示**

```
以带有标签的 JSON 数组形式返回边界框。切勿返回掩码或代码围栏。限制为 25 个对象。包括您可以在桌子上识别的所有对象。
如果一个对象多次出现，请根据其独特的特征（颜色、大小、位置、独特特征等）对其进行命名。
格式应如下所示：[{"box_2d": [ymin, xmin, ymax, xmax], "label": <object_label>}]，归一化到 0-1000。box_2d 中的值必须仅为整数
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 9) 简单轨迹规划

<img width="794" height="444" alt="image" src="https://github.com/user-attachments/assets/5c2f94dc-9b08-4e2b-b1d3-58ca55c6fb51" />

**提示**

```
在红笔上放置一个点，然后为将红笔移动到左侧收纳盒顶部的轨迹放置 15 个点。
这些点应按轨迹顺序标记，从“0”（左手的起点）到 <n>（终点）
答案应遵循 json 格式：[{"point": <point>, "label": <label1>}, ...]。
点采用 [y, x] 格式，归一化到 0-1000。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 10) 刷除颗粒的路径

<img width="792" height="592" alt="image" src="https://github.com/user-attachments/assets/855c0a41-29f7-4d3b-a774-c22e4d726ac4" />

**提示**

```
指向蓝色刷子和覆盖颗粒区域的 10 个点的列表。
确保这些点均匀分布在颗粒上，以创建平滑的轨迹。
根据清洁盘子轨迹中应接近的顺序，将点从 1 到 10 标记。
移动应从刷子开始。
答案应遵循 json 格式：[{"point": <point>, "label": <label1>}, ...]。
点采用 [y, x] 格式，归一化到 0-1000。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 11) 避障轨迹规划

<img width="801" height="743" alt="image" src="https://github.com/user-attachments/assets/53cfab6e-db07-4eb7-b325-27d60da55eee" />

**提示**

```
在当前视图原点和左后方的绿色搁脚凳之间找到地板上最直接的无碰撞轨迹的 10 个点。这些点应避开地板上的所有其他障碍物。
答案应遵循 json 格式：[{"point": <point>, "label": <label1>}, ...]。
点采用 [y, x] 格式，归一化到 0-1000。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 12) 为笔记本电脑腾出空间要移除的物品

<img width="795" height="598" alt="image" src="https://github.com/user-attachments/assets/70a17400-d409-4ec9-8c23-4a2595f528ec" />

**提示**

```
指向我需要移除以便为我的笔记本电脑腾出空间的对象
答案应遵循 json 格式：[{"point": <point>, "label": <label1>}, ...]。点采用 [y, x] 格式，归一化到 0-1000。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 13) 编排：打包午餐

<img width="1207" height="855" alt="image" src="https://github.com/user-attachments/assets/aab2f569-99fe-4d7e-ae34-159655729a59" />

**提示**

```
解释如何打包午餐盒和午餐袋。指向您引用的每个对象。每个点应采用以下格式：[{"point": [y, x], "label": }]，其中坐标在 0-1000 之间归一化。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 14) 空的电源插座

<img width="792" height="938" alt="image" src="https://github.com/user-attachments/assets/469d73df-3b01-48d6-b1f5-e39b62aa5133" />

**提示**

```
指向无遮挡的空插座
答案应遵循 json 格式：[{"point": <point>, "label": <label1>}, ...]。点采用 [y, x] 格式，归一化到 0-1000。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 15) 限制物品举升（3 磅限制）

<img width="792" height="954" alt="image" src="https://github.com/user-attachments/assets/a45216e1-cac7-4217-b51e-98d0d23e8754" />

**提示**

```
我是一个有效载荷为 3 磅的机器人。指向图像中我能够捡起的所有物体。
答案应遵循 json 格式：[{"point": <point>, "label": <label1>}, ...]。点采用 [y, x] 格式，归一化到 0-1000。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 16) 视频分析

https://github.com/user-attachments/assets/cc2b3c62-2ce6-4c7b-bcf8-968f000e37f5

**提示**

```
详细描述完成任务的每个步骤。按时间戳分解，以 json 格式输出，键为“start_timestamp”、“end_timestamp”和“description”。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 17) 视频分析：时间范围

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
放大到第 15 秒到 22 秒，并以相同的格式提供每秒的事件分解。
"""

start_time = time.time()

response = chat.send_message(
    [prompt, myfile]
)

end_time = time.time()
elapsed_time = end_time - start_time
print(f"\n总处理时间：{elapsed_time:.4f} 秒")

print(response.text)
```

总处理时间：11.4140 秒
```json
[
  {
    "start_timestamp": "00:15",
    "end_timestamp": "00:16",
    "description": "左机械臂开始从桌子上方的​​位置向蓝色笔移动。"
  },
  {
    "start_timestamp": "00:16",
    "end_timestamp": "00:18",
    "description": "左机械臂将其夹爪直接定位在桌子上的蓝色笔上方。"
  },
  {
    "start_timestamp": "00:18",
    "end_timestamp": "00:19",
    "description": "左机械臂降低其夹爪并抓住蓝色笔。"
  },
  {
    "start_timestamp": "00:19",
    "end_timestamp": "00:20",
    "description": "左机械臂将蓝色笔从桌子表面提起。"
  },
  {
    "start_timestamp": "00:20",
    "end_timestamp": "00:21",
    "description": "左机械臂将蓝色笔移向桌子中央，将其定位在黑色网状笔筒上方。"
  },
  {
    "start_timestamp": "00:21",
    "end_timestamp": "00:22",
    "description": "左机械臂松开蓝色笔，将其放入黑色网状笔筒中。"
  }
]
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 18) 找到第四排架子

<img width="794" height="922" alt="image" src="https://github.com/user-attachments/assets/f7d26839-67a8-4215-b23d-e3eeb89ff11e" />

**提示**

```
以 JSON 数组的形式返回带有标签的边界框，突出显示第四排架子中的所有隔间。
格式应如下所示：[{"box_2d": [ymin, xmin, ymax, xmax], "label": <object_label>}]，归一化到 0-1000。box_2d 中的值必须仅为整数
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 19) 寻找有特定物品的架子

<img width="794" height="942" alt="image" src="https://github.com/user-attachments/assets/95baca11-e7aa-485c-a927-0e392d9f6390" />

**提示**

```
“我需要擤鼻子。”
找到可以提供帮助的隔间。
格式应如下所示：[{"box_2d": [ymin, xmin, ymax, xmax], "label": <object_label>}]，归一化到 0-1000。box_2d 中的值必须仅为整数
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 20) 带思考地数数物品

<img width="960" height="600" alt="image" src="https://github.com/user-attachments/assets/d902b60a-006e-406a-8641-611240dd9f87" />

**输出**

```
根据对图像前景中购物车篮子的目视检查，里面有 **8 件物品**。

以下是计数的推理：

1.  **堆叠的盒子：** 篮子左侧有一堆几个盒子。我们可以清楚地识别出这堆中至少有五个盒子：
    *   左下角有一个蓝色的 Barilla 意大利面盒子。
    *   Barilla 盒子旁边有一个黄色的 Wheat Thins 盒子。
    *   在这两个盒子上面至少还有三个盒子，部分可见。
2.  **罐头：** 篮子右下角可以看到两个银色罐头。
3.  **小包：** 篮子中央有一个蓝色小包，位于盒子和罐头之间。
```

**提示**

```
购物车篮子里有多少件物品。请分享你的推理。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 21) 寻找有特定物品的架子

| ![initial_state_1](https://storage.googleapis.com/generativeai-downloads/images/robotics/er-1-5-example-colab/initial_state_1.png) | ![initial_state_2](https://storage.googleapis.com/generativeai-downloads/images/robotics/er-1-5-example-colab/initial_state_2.png) | ![initial_state_3](https://storage.googleapis.com/generativeai-downloads/images/robotics/er-1-5-example-colab/initial_state_3.png) | ![initial_state_4](https://storage.googleapis.com/generativeai-downloads/images/robotics/er-1-5-example-colab/initial_state_4.png) |
|---|---|---|---|
| ![current_state_1](https://storage.googleapis.com/generativeai-downloads/images/robotics/er-1-5-example-colab/current_state_1.png) | ![current_state_2](https://storage.googleapis.com/generativeai-downloads/images/robotics/er-1-5-example-colab/current_state_2.png) | ![current_state_3](https://storage.googleapis.com/generativeai-downloads/images/robotics/er-1-5-example-colab/current_state_3.png) | ![current_state_4](https://storage.googleapis.com/generativeai-downloads/images/robotics/er-1-5-example-colab/current_state_4.png) |


**输出**

成功？否

**提示**

```
对于此任务，您将看到一个机器人或人类试图执行将芒果放入棕色容器的任务。
您可能会看到同一场景的多个摄像机视图。一些摄像机是静态的
并且安装在场景外部，一些摄像机安装在机器人手臂上
因此它们在情节中移动。前 4 张图像显示了多个
来自情节开始（一段时间前）的摄像机视图。最后 4 张图像显示了多个
来自情节当前时刻（现在）的摄像机视图。
看着这些图像并比较情节的开始和当前状态
机器人是否成功执行了“将芒果放入棕色容器”的任务？
仅回答 (1) 是或 (2) 否。返回最能回答问题的数字 (1) 或 (2)。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 22) 放大图像的各个部分以获得更好的读数

<img width="794" height="959" alt="image" src="https://github.com/user-attachments/assets/338a9381-8f71-4f61-a378-a57a9bc64f75" />

**提示**

```
空气质量读数是多少？使用代码执行功能，放大图像以仔细查看。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 23) 机器人夹爪和物品的分割

<img width="796" height="456" alt="image" src="https://github.com/user-attachments/assets/09403f45-f6e7-4fd1-8390-4614e3481e3a" />

**提示**

```
提供此图像中以下对象的分割掩码：芒果、左机器人夹爪手指、右机器人夹爪手指。
答案应遵循 JSON 格式：
[
  {
    "box_2d": [ymin, xmin, ymax, xmax],
    "label": "<object_label>",
    "mask": "data:image/png;base64,<base64 encoded PNG mask>"
  },
  ...
]
box_2d 坐标应归一化到 0-1000，并且必须是整数。
掩码应为 base64 编码的 PNG 图像，其中非零像素表示掩码。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 24) 定位相关对象

<img width="793" height="504" alt="image" src="https://github.com/user-attachments/assets/69d4df26-a15d-4fcb-8ad7-d0b0cf9889b5" />

**提示**

```
定位并指向蓝色块和橙色碗。返回的
标签应该是检测到的对象的识别名称。
答案应遵循 json 格式：[{"point": <point>, "label": <label1>}, ...]。
点采用 [y, x] 格式，归一化到 0-1000。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 25) 厨房场景：**指点和命名对象**（2D 点）✅

![厨房指点](https://storage.googleapis.com/gweb-developer-goog-blog-assets/images/unnamed-2_2.original.png)

**提示**

```
在图像中指向以下物品：洗洁精、碗碟架、水龙头、电饭煲、独角兽。
点采用 [y, x] 格式，归一化到 0-1000。
仅包括图像中实际存在的对象。
返回：[{"point":[y,x],"label":"<name>"}]
```

ER 1.5 返回精确的、语义上接地的点；与您的深度/姿态堆栈结合以进行运动规划。([谷歌开发者博客])

---

### 26) 视频的时间推理：**什么时间发生了什么？** 🧩




https://github.com/user-attachments/assets/5c55cbea-6946-4d8f-8e33-a17c4f0d6dd7




**提示**

```
您会看到一个简短的机器人操作视频。
使用精确的时间戳描述每个步骤，格式为 JSON：
[{"start_timestamp":"MM:SS","end_timestamp":"MM:SS","description":"..."}]
```

ER 1.5 可以推理**顺序、因果关系和间隔**（例如，“将绿色记号笔放入托盘，然后将蓝色/红色笔放入杯子”），甚至可以放大到子间隔。用它来验证执行并注释片段。([谷歌开发者博客])

---

### 27) 咖啡机：**规划关闭轨迹**（多点路径）✅

![咖啡盖轨迹](https://storage.googleapis.com/gweb-developer-goog-blog-assets/images/image1_PCWZQMD.original.png)

**提示**

```
现在我需要关闭咖啡机。
绘制一个由 8 个点组成的轨迹，指示手柄应如何移动以关闭它。
从手柄开始。点为 [Y,X]，归一化为 0-1000。
返回：
[{"point":[Y,X],"label":"p0"}, {"point":[Y,X],"label":"p1"}, ...]
```

使用输出为您的运动规划器提供种子（平滑、碰撞检查）。([谷歌开发者博客])

---

### 28) 咖啡清理：**我应该把杯子放在哪里？**（功能可见性指向）✅

![把杯子放好](https://storage.googleapis.com/gweb-developer-goog-blog-assets/images/image12_O0rW8PY.original.png)

**提示**

```
我喝完咖啡了。现在我应该把我的杯子放在哪里来清理？
返回 [{"point":[y,x],"label":"<best place>"}]，y/x 归一化为 0-1000。
```

非常适合任务完成后**功能可见性感知放置**。([谷歌开发者博客])

---

### 29) **按当地规定进行垃圾分类**（工具使用 + 空间定位）🧩

https://github.com/user-attachments/assets/503b20d6-1995-4e71-9164-9a79d6bf6a4c

**提示**

```
使用谷歌搜索工具获取我所在城市最新的回收/堆肥规则。
然后，根据此图像，解释如何将每个可见物品分类到正确的垃圾箱中。
使用 [{"point":[y,x],"label":"<bin>"}] 指向每个引用的物品，归一化为 0-1000。
返回一个我可以交给我的 VLA 控制器的分步计划。
```

ER 1.5 可以**调用工具**（例如，谷歌搜索）并输出**交错的文本 + 点**以用于“空间定位”的计划。([谷歌开发者博客])

---

### 30) **根据参考照片重新整理我的办公桌**（长期计划）🧩

**提示**

```
给定 (A) 我当前的办公桌照片和 (B) 一张参考照片，
创建一个分步的重组计划。
对于每个步骤，包括目标放置为 [{"box_2d":[ymin,xmin,ymax,xmax],"label":"<item>"}]，
归一化为 0-1000，以及 1-2 句的理由。
```

演示了在许多步骤中具有**空间定位**的**代理规划**。([谷歌开发者博客])

---

### 31) **为笔记本电脑腾出空间**（选择要移动的物品）🧩

![腾出空间](https://ai.google.dev/gemini-api/docs/images/robotics/spatial-reasoning.png)

**提示**

```
指向我应该移除的单个物体，以便为我的笔记本电脑腾出空间。
返回 [{"point":[y,x],"label":"<要移动的物体>"}]，归一化为 0-1000。
```

文档中的一个典型的**编排**示例——推理约束并选择最小的更改。([Google AI for Developers])

---

### 32) **用空间定位的说明打包午餐** ✅

![打包午餐](https://ai.google.dev/static/gemini-api/docs/images/robotics/packing-lunch.png)

**提示**

```
解释如何从这张图片中打包午餐盒和午餐袋。
指向您引用的每个对象：
[{"point":[y,x],"label":"<name>"}]，归一化为 0-1000。
返回一个带编号的分步计划。
```

输出一个**多步计划**以及您可以渲染或转换为拾取和放置调用的点。([Google AI for Developers])

---

### 33) **通过您的机器人 API 进行拾取和放置**（函数调用）🧩

![积木和碗](https://ai.google.dev/gemini-api/docs/images/robotics/robot-api-example.png)

**提示**

```
首先，使用以下命令定位蓝色积木和橙色碗：
[{"point":[y,x],"label":"<name>"}]，0-1000。
然后，仅使用以下函数：
  def move(x, y, high: bool)
  def setGripperState(opened: bool)
  def returnToOrigin()
生成精确的调用序列以拾取积木并将其放入碗中。
简要解释每个调用。
```

文档端到端地展示了这种模式（检测 → 计划 → 调用您的 API）。([Google AI for Developers])

---

### 34) **将红笔移动到收纳盒**（轨迹规划）🧩

![笔的轨迹](https://ai.google.dev/gemini-api/docs/images/robotics/trajectories.png)

**提示**

```
在红笔上放置一个点，然后为其到收纳盒的轨迹放置 15 个点。
按顺序“0”...“15”标记点。返回 [{"point":[y,x],"label":"<step>"}]。
```

非常适合**通过轨迹进行教学**和下游样条拟合/时间参数化。([Google AI for Developers])

---

### 35) **按颜色分类衣物**（多层次思考）🧩

[![](https://i.imgur.com/2fqyZh6.png)](https://www.youtube.com/watch?v=eDyXEh8XqjM&t=34s)


**提示**

```
将衣物按颜色分类到两个篮子中：白色与非白色。
返回一个简短的自然语言计划，并为每件衣物返回一个
{"point":[y,x],"label":"white|non-white"}，带有归一化的 2D 坐标。
```

官方博客解释了 Robotics 1.5/ER 1.5 如何在任务/子任务/微观操作级别上思考诸如**按颜色分类衣物**之类的家务。([blog.google])

---

### 36) **考虑有效载荷的拾取**（安全和约束）🧩


**提示**

```
计划一个拾取和放置最重可见物品的方案
受 1.0 公斤有效载荷限制和 25 厘米伸展半径的约束。
解释约束检查，如果不安全则拒绝。
返回 [{"point":[y,x],"label":"grasp"}] 和“原因”。
```

ER 1.5 增加了改进的语义安全性和对物理约束的认识；您仍必须实施硬件级安全。([谷歌开发者博客])

---

### 37) **人机自然语言**：“清理桌子” 🧩

![清理桌子](https://storage.googleapis.com/gweb-developer-goog-blog-assets/images/image12_O0rW8PY.original.png)

**提示**

```
给定一个桌子场景图像，制定一个带编号的清理计划。
对于每个步骤，包括一个目标放置点或 2D 框以及关于成功标准的说明。
格式：
{"step":n,"action":"...","target":{"point":[y,x] | "box_2d":[ymin,xmin,ymax,xmax]},"why":"..."}
```

ER 1.5 将模糊的命令分解为可执行的子目标，并可以调用专门的抓取/VLA 技能来执行。([谷歌开发者博客])

---

### 38) **跨实体规划**（与机器人无关的步骤）🧩

[![Embodiments](https://github.com/user-attachments/assets/919be470-810b-4974-8fe9-2784623aabd2)](https://www.youtube.com/watch?v=9FV5ZYytkOQ)

**提示**

```
从这个工作站场景中，生成一个与机器人无关的计划
仅用环境/功能可见性术语表示（无关节空间）。
假设它将在 ALOHA 2、Apptronik Apollo 或 Franka 臂上执行。
返回一个包含成功检查和后备选项的步骤列表。
```

DeepMind 强调了跨实体（例如，ALOHA-2 → Apollo/Franka）的泛化，以加速技能转移。([blog.google])

---

## 提示和模式

* 对于点，首选**归一化的 [y,x]（范围 0–1000）**，对于框，首选 `[ymin,xmin,ymax,xmax]`。这使得提示对模型友好且与实现无关。([谷歌开发者博客])
* 根据任务的复杂性调整**思考预算**（延迟/准确性权衡）。([谷歌开发者博客])
* 交错**文本 + 点/框/轨迹**以生成您的控制器可以执行的“空间定位”计划。([谷歌开发者博客])
* 使用**工具调用**（例如，搜索）将计划建立在当地规则（回收、厨房政策等）的基础上。([谷歌开发者博客])

---

## 贡献

* 在 `cases/<short-name>/` 下添加一个新文件夹，其中包含：

  * `README.md`（1-2 句话 + 提示）
  * `image.jpg/png`（或链接）
* 保持提示**可复制运行**且**JSON 友好**。
* 引用您的来源（首选主要文档/博客/视频）。

---

## 许可证和图像归属

* 文本：MIT（本仓库）。
* 标记为 ✅ 的图像来自谷歌的公共文档/博客，此处仅用作**演示参考**；在重新分发之前，请检查源许可证。用您自己的图像替换 🧩 占位符。

**主要来源**

* 谷歌开发者博客 — *使用 Gemini Robotics-ER 1.5 构建下一代物理智能体*（官方提示、图像、功能）。([谷歌开发者博客])
* Google AI for Developers — *Gemini Robotics-ER 1.5（机器人技术概述）*（点、轨迹、编排、代码）。([Google AI for Developers])
* 谷歌/DeepMind 博客 — ER 1.5 发布背景、具身推理、跨实体；JP 博客包括洗衣分类示例和合作伙伴机器人参考。([Google DeepMind])

---

> 💡 将此文件放入
> `https://github.com/GitHub30/Awesome-Gemini-Robotics/blob/main/README.md`
> 并开始在 `assets/` 下添加您自己的屏幕截图以替换 🧩 占位符。

## 🙏 致谢

本仓库中的各种案例依赖于 AI 社区的分享。请允许我们向所有案例贡献者表示诚挚的谢意。

感谢以下用户分享他们出色的作品。您也可以访问他们的个人资料以了解更多信息：

- [@GoogleDeepMind](https://x.com/GoogleDeepMind)
- [@GeminiApp](https://x.com/GeminiApp)

*我们不能保证所有案例都来自原作者。如果这给您带来任何不便，请随时与我们联系进行修改。*

我们收集的案例无法涵盖所有可能的应用场景。如果您有其他有趣的发现 🔍，我们欢迎您与我们联系以展示更多创意 📧！

[![Star History Chart](https://api.star-history.com/svg?repos=GitHub30/Awesome-Gemini-Robotics&type=Date)](https://www.star-history.com/#GitHub30/Awesome-Gemini-Robotics&Date)

: https://developers.googleblog.com/en/building-the-next-generation-of-physical-agents-with-gemini-robotics-er-15/ "使用 Gemini Robotics-ER 1.5 构建下一代物理智能体 - 谷歌开发者博客"
: https://ai.google.dev/gemini-api/docs/robotics-overview "Gemini Robotics-ER 1.5 | Gemini API | Google AI for Developers"
: https://blog.google/intl/ja-jp/company-news/technology/gemini-robotics-15-ai/ "宣布推出 Gemini Robotics 1.5，将 AI 智能体带入物理世界"
: https://deepmind.google/discover/blog/gemini-robotics-15-brings-ai-agents-into-the-physical-world/ "Gemini Robotics 1.5 将 AI 智能体带入物理世界 - 谷歌 DeepMind"
