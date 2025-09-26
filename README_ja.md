<div align="center">
<img width="3334" height="992" alt="image" src="https://github.com/user-attachments/assets/32195746-89c3-4e33-a93d-00a7f0642f79" />

[![ライセンス: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![English](https://img.shields.io/badge/English-Click_to_View-yellow)](README.md)
[![Chinese](https://img.shields.io/badge/汉语-撳一下就可以睇到-orange)](README_zh.md)
[![Japanese](https://img.shields.io/badge/日本語-クリックして表示-green)](README_ja.md)
[![Korean](https://img.shields.io/badge/한국어-눌러서_보기-blue)](README_kr.md)
[![Vietnamese](https://img.shields.io/badge/tiếng_Việt-Nhấp_để_xem-blueviolet)](README_vn.md)
</div>

# Awesome Gemini Robotics

**Gemini Robotics-ER 1.5**（実体化された推論、VLM）の例とプロンプトを集めた、コミュニティが維持する厳選ギャラリーです。自分のプロジェクトにコピー＆ペーストして使用できます。各カードには、**代表的な画像**と**すぐに使えるプロンプト**（多くはJSONフレンドリー）が含まれており、「Awesome Nano Banana Images」のスタイルを踏襲しています。
何かクールなものを作ったら、プルリクエストを送ってください！

> **Gemini Robotics-ER 1.5とは？**
> Googleの最先端のロボット工学向け実体化推論モデルです。ER 1.5は、**空間理解（2Dポイント、ボックス、軌跡）**、**長期的タスクプランニング**、**ビデオの時系列推論**、**ツールの使用（例：Google検索）**、**成功/進捗推定**に優れています。Google AI Studio / Gemini APIを介して直接呼び出し、独自のロボットAPIやVLAを編成することができます。（[Google Developers Blog]）

---

## 目次

* [クイックスタート](#クイックスタート)
* [ユースケース（10件以上）](#ユースケース)
* [貢献](#貢献)
* [ライセンスと画像の帰属](#ライセンスと画像の帰属)

---

## クイックスタート

> Gemini APIを使用した最小限のPythonスケッチ（モデル名は早期アクセスで`gemini-robotics-er-1.5-preview`として表示される場合があります）：

```python
from google import genai
from google.genai import types

client = genai.Client()
MODEL_ID = "gemini-robotics-er-1.5-preview"

prompt = """
画像内の最大10個のアイテムを指し示してください。
y/xを0-1000に正規化して、[{"point":[y,x],"label":"<name>"}]を返してください。
"""

img_bytes = open("scene.jpg","rb").read()
res = client.models.generate_content(
    model=MODEL_ID,
    contents=[types.Part.from_bytes(data=img_bytes, mime_type="image/jpeg"), prompt],
    config=types.GenerateContentConfig(
        temperature=0.5,
        thinking_config=types.ThinkingConfig(thinking_budget=0) # 必要に応じて調整
    )
)
print(res.text)
```

これは、Googleの公式ロボティクス概要（ポイント、軌跡、オーケストレーション、ビデオなど）を反映しています。その他の例（オブジェクト追跡、昼食の準備、カスタムロボットAPIの呼び出し）については、ドキュメントを参照してください。（[Google AI for Developers]）

---

## ユースケース

> ✅ = Googleのドキュメント/ブログからの公開デモ画像を含む • 🧩 = 独自の画像を持参（プレースホルダーパス）

### 1) 未定義のオブジェクトを指す

<img width="801" height="598" alt="image" src="https://github.com/user-attachments/assets/35881c79-922e-43dd-9d1c-b22e4f66715d" />

**プロンプト**

```
画像内の最大10個のアイテムを指し示してください。返されるラベルは、検出されたオブジェクトを識別する名前である必要があります。
回答は、json形式に従う必要があります：[{"point": <point>, "label": <label1>}, ...]。ポイントは、0-1000に正規化された[y, x]形式です。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 2) 定義済みのオブジェクトを指す

<img width="800" height="601" alt="image" src="https://github.com/user-attachments/assets/8bf517fa-a094-40c9-be65-6c7bedfde295" />

**プロンプト**

```
次のオブジェクトに一致するすべてのポイントを取得します：パン、スターフルーツ、バナナ。返されるラベルは、検出されたオブジェクトを識別する名前である必要があります。
回答は、json形式に従う必要があります：[{"point": <point>, "label": <label1>}, ...]。ポイントは、0-1000に正規化された[y, x]形式です。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 3) より抽象的な説明（例：「果物」）に基づいてオブジェクトのすべてのインスタンスを指す

<img width="798" height="597" alt="image" src="https://github.com/user-attachments/assets/974995c1-9a92-4c04-9e89-3bcbefdd22d8" />

**プロンプト**

```
果物のすべてのポイントを取得します。返されるラベルは、検出されたオブジェクトを識別する名前である必要があります。
回答は、json形式に従う必要があります：[{"point": <point>, "label": <label1>}, ...]。ポイントは、0-1000に正規化された[y, x]形式です。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 4) オブジェクトのすべてのインスタンスを指す

<img width="799" height="450" alt="image" src="https://github.com/user-attachments/assets/93382594-8156-43ff-9864-aa627346b2ae" />

**プロンプト**

```
ゲームボードスロットに一致するすべてのポイントを取得します。返されるラベルは、検出されたオブジェクトを識別する名前である必要があります。
回答は、json形式に従う必要があります：[{"point": <point>, "label": <label1>}, ...]。ポイントは、0-1000に正規化された[y, x]形式です。
```

```
Xゲームピースに一致するすべてのポイントを取得します。返されるラベルは、検出されたオブジェクトを識別する名前である必要があります。
回答は、json形式に従う必要があります：[{"point": <point>, "label": <label1>}, ...]。ポイントは、0-1000に正規化された[y, x]形式です。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 5) オブジェクトの特定の部分を連続して指す

<img width="795" height="592" alt="image" src="https://github.com/user-attachments/assets/68c6bd2f-a064-43e4-bee3-a377a33d73c7" />

**プロンプト**

```python
queries = [
    ("紙袋", "取っ手"),
    ("バナナ", "茎"),
    ("バナナ", "中央"),
    ("スターフルーツ", "中央"),
    ("ライム", "中央"),
    ("水色のボウル", "縁"),
    ("濃い青のボウル", "縁"),
    ("計量カップ", "縁"),
    ("計量カップ", "取っ手"),
    ("ボウル", "トマト"),
]

画像内の$objectの$partを指し示してください。回答を、キーが「point」と「label」の辞書のjsonリストとして返してください。このリクエストに対しては1つのポイントのみを返してください。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 6) 指差しによるカウント

<img width="793" height="820" alt="image" src="https://github.com/user-attachments/assets/2592ca4e-c1a9-4510-a251-c63dbbeaa52d" />

**プロンプト**

```
箱の中の各ワッシャーを指し示してください。
回答は、json形式に従う必要があります：[{"point": <point>, "label": <label1>}, ...]。ポイントは、0-1000に正規化された[y, x]形式です。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 7) GIF内の定義済みオブジェクトを指す

![ダウンロード (1)](https://github.com/user-attachments/assets/635a748f-eae9-44c3-88b8-31627eec0ed8)

**プロンプト**

```python
queries = [
    "ペン（机の上）",
    "ペン（ロボットの手の中）",
    "ラップトップ（開いている）",
    "ラップトップ（閉じている）",
]

提供された画像内の次のオブジェクトを指し示してください：{', '.join(queries)}。
回答は、json形式に従う必要があります：[{"point": <point>, "label": <label1>}, ...]。
ポイントは、0-1000に正規化された[y, x]形式です。
オブジェクトが見つからない場合は、空のJSONリスト[]を返してください。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 8) 2Dバウンディングボックス

<img width="794" height="597" alt="image" src="https://github.com/user-attachments/assets/afaa8594-e682-4aaa-aa4c-62c468a66e49" />

**プロンプト**

```
バウンディングボックスをラベル付きのJSON配列として返してください。マスクやコードフェンスは絶対に返さないでください。オブジェクトは25個に制限してください。テーブル上で識別できる限り多くのオブジェクトを含めてください。
オブジェクトが複数存在する場合は、その独自の特徴（色、サイズ、位置、独自の特徴など）に応じて名前を付けてください。
形式は次のようになります：[{"box_2d": [ymin, xmin, ymax, xmax], "label": <オブジェクトのラベル>}] 0-1000に正規化します。box_2dの値は整数でなければなりません。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 9) シンプルな軌道計画

<img width="794" height="444" alt="image" src="https://github.com/user-attachments/assets/5c2f94dc-9b08-4e2b-b1d3-58ca55c6fb51" />

**プロンプト**

```
赤いペンに点を置き、次に赤いペンを左のオーガナイザーの上に移動するための軌跡の15点を置きます。
点は、軌跡の順序に従って、「0」（左手の開始点）から<n>（最終点）までラベル付けする必要があります。
回答は、json形式に従う必要があります：[{"point": <point>, "label": <label1>}, ...]。
ポイントは、0-1000に正規化された[y, x]形式です。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 10) 粒子をブラッシングするためのパス

<img width="792" height="592" alt="image" src="https://github.com/user-attachments/assets/855c0a41-29f7-4d3b-a774-c22e4d726ac4" />

**プロンプト**

```
青いブラシと、粒子の領域をカバーする10個の点のリストを指し示してください。
滑らかな軌道を作成するために、点が粒子上に均等に広がるようにしてください。
プレートを掃除する軌道でアプローチされるべき順序に基づいて、点に1から10までのラベルを付けます。
動きはブラシから開始する必要があります。
回答は、json形式に従う必要があります：[{"point": <point>, "label": <label1>}, ...]。
ポイントは、0-1000に正規化された[y, x]形式です。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 11) 障害物回避軌道計画

<img width="801" height="743" alt="image" src="https://github.com/user-attachments/assets/53cfab6e-db07-4eb7-b325-27d60da55eee" />

**プロンプト**

```
現在の視点の原点と左奥の緑のオットマンとの間の床上の10点の最も直接的な衝突のない軌道を見つけてください。点は、床上の他のすべての障害物を避ける必要があります。
回答は、json形式に従う必要があります：[{"point": <point>, "label": <label1>}, ...]。
ポイントは、0-1000に正規化された[y, x]形式です。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 12) ラップトップ用のスペースを確保するために削除するアイテム

<img width="795" height="598" alt="image" src="https://github.com/user-attachments/assets/70a17400-d409-4ec9-8c23-4a2595f528ec" />

**プロンプト**

```
ラップトップ用のスペースを確保するために削除する必要があるオブジェクトを指し示してください。
回答は、json形式に従う必要があります：[{"point": <point>, "label": <label1>}, ...]。ポイントは、0-1000に正規化された[y, x]形式です。```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 13) オーケストレーション：ランチを詰める

<img width="1207" height="855" alt="image" src="https://github.com/user-attachments/assets/aab2f569-99fe-4d7e-ae34-159655729a59" />

**プロンプト**

```
ランチボックスとランチバッグの詰め方を説明してください。参照する各オブジェクトを指し示してください。各ポイントは、[{"point": [y, x], "label": }] の形式である必要があります。ここで、座標は0〜1000の間で正規化されます。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 14) 空の電気ソケット

<img width="792" height="938" alt="image" src="https://github.com/user-attachments/assets/469d73df-3b01-48d6-b1f5-e39b62aa5133" />

**プロンプト**

```
遮られていない空のソケットを指し示してください。
回答は、json形式に従う必要があります：[{"point": <point>, "label": <label1>}, ...]。ポイントは、0-1000に正規化された[y, x]形式です。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 15) アイテムリフトの制限（3LB制限）

<img width="792" height="954" alt="image" src="https://github.com/user-attachments/assets/a45216e1-cac7-4217-b51e-98d0d23e8754" />

**プロンプト**

```
私はペイロードが3ポンドのロボットです。画像内で物理的に持ち上げることができるすべてのオブジェクトを指し示してください。
回答は、json形式に従う必要があります：[{"point": <point>, "label": <label1>}, ...]。ポイントは、0-1000に正規化された[y, x]形式です。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 16) ビデオ分析

https://github.com/user-attachments/assets/cc2b3c62-2ce6-4c7b-bcf8-968f000e37f5

**プロンプト**

```
タスクを完了するための各ステップを詳細に説明してください。タイムスタンプで分類し、「start_timestamp」、「end_timestamp」、「description」をキーとするjson形式で出力してください。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 17) ビデオ分析：時間範囲

```python
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
15秒から22秒にズームインし、同じ形式で何が起こっているかを1秒ごとに詳細に説明してください。
"""

start_time = time.time()

response = chat.send_message(
    [prompt, myfile]
)

end_time = time.time()
elapsed_time = end_time - start_time
print(f"\n合計処理時間：{elapsed_time:.4f}秒")

print(response.text)
```

合計処理時間：11.4140秒
```json
[
  {
    "start_timestamp": "00:15",
    "end_timestamp": "00:16",
    "description": "左のロボットアームがテーブルの上の位置から青いペンに向かって動き始めます。"
  },
  {
    "start_timestamp": "00:16",
    "end_timestamp": "00:18",
    "description": "左のロボットアームが、テーブルの上の青いペンの真上にグリッパーを配置します。"
  },
  {
    "start_timestamp": "00:18",
    "end_timestamp": "00:19",
    "description": "左のロボットアームがグリッパーを下げ、青いペンをつかみます。"
  },
  {
    "start_timestamp": "00:19",
    "end_timestamp": "00:20",
    "description": "左のロボットアームがテーブルの表面から青いペンを持ち上げます。"
  },
  {
    "start_timestamp": "00:20",
    "end_timestamp": "00:21",
    "description": "左のロボットアームは青いペンをテーブルの中央に向かって動かし、黒いメッシュのペンホルダーの上に配置します。"
  },
  {
    "start_timestamp": "00:21",
    "end_timestamp": "00:22",
    "description": "左のロボットアームが青いペンを離し、黒いメッシュのペンホルダーに落とします。"
  }
]
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 18) 4段目の棚を見つける

<img width="794" height="922" alt="image" src="https://github.com/user-attachments/assets/f7d26839-67a8-4215-b23d-e3eeb89ff11e" />

**プロンプト**

```
棚の4段目にあるすべての仕切りをハイライト表示するラベル付きのJSON配列としてバウンディングボックスを返します。
形式は次のようになります：[{"box_2d": [ymin, xmin, ymax, xmax], "label": <オブジェクトのラベル>}] 0-1000に正規化します。box_2dの値は整数でなければなりません。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 19) 特定のアイテムがある棚を見つける

<img width="794" height="942" alt="image" src="https://github.com/user-attachments/assets/95baca11-e7aa-485c-a927-0e392d9f6390" />

**プロンプト**

```
「鼻をかむ必要があります。」
役に立つ仕切りを見つけてください。
形式は次のようになります：[{"box_2d": [ymin, xmin, ymax, xmax], "label": <オブジェクトのラベル>}] 0-1000に正規化します。box_2dの値は整数でなければなりません。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 20) 思考を伴うアイテムのカウント

<img width="960" height="600" alt="image" src="https://github.com/user-attachments/assets/d902b60a-006e-406a-8641-611240dd9f87" />

**出力**

```
画像の前景にあるショッピングカートのバスケットを目視で検査したところ、中に**8個のアイテム**があります。

カウントの理由は次のとおりです。

1.  **積み重ねられた箱：**バスケットの左側にいくつかの箱の山があります。この山には少なくとも5つの箱がはっきりと確認できます。
    *   左下に青いバリラのパスタの箱。
    *   バリラの箱の隣に黄色いウィートシンズの箱。
    *   これら2つの上に積み重ねられ、部分的に見える少なくとも3つ以上の箱。
2.  **缶：**バスケットの右下に2つの銀色の缶が見えます。
3.  **パケット：**バスケットの中央、箱と缶の間に小さな青いパケットが1つあります。
```

**プロンプト**

```
カートのバスケットの中にいくつのアイテムがありますか？あなたの推論を共有してください。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 21) 特定のアイテムがある棚を見つける

| ![initial_state_1](https://storage.googleapis.com/generativeai-downloads/images/robotics/er-1-5-example-colab/initial_state_1.png) | ![initial_state_2](https://storage.googleapis.com/generativeai-downloads/images/robotics/er-1-5-example-colab/initial_state_2.png) | ![initial_state_3](https://storage.googleapis.com/generativeai-downloads/images/robotics/er-1-5-example-colab/initial_state_3.png) | ![initial_state_4](https://storage.googleapis.com/generativeai-downloads/images/robotics/er-1-5-example-colab/initial_state_4.png) |
|---|---|---|---|
| ![current_state_1](https://storage.googleapis.com/generativeai-downloads/images/robotics/er-1-5-example-colab/current_state_1.png) | ![current_state_2](https://storage.googleapis.com/generativeai-downloads/images/robotics/er-1-5-example-colab/current_state_2.png) | ![current_state_3](https://storage.googleapis.com/generativeai-downloads/images/robotics/er-1-5-example-colab/current_state_3.png) | ![current_state_4](https://storage.googleapis.com/generativeai-downloads/images/robotics/er-1-5-example-colab/current_state_4.png) |

**出力**

成功？いいえ

**プロンプト**

```
このタスクでは、マンゴーを茶色の容器に入れるタスクを実行しようとしているロボットまたは人間が表示されます。
同じシーンの複数のカメラビューが表示される場合があります。一部のカメラは静的で、
シーンの外に取り付けられており、一部のカメラはロボットアームに取り付けられています。
したがって、エピソード中に移動します。最初の4つの画像は、
エピソードの開始時（少し前）からの複数のカメラビューを示しています。最後の4つの画像は、
エピソードの現在の瞬間（現在）からの複数のカメラビューを示しています。
これらの画像を調べて、エピソードの開始時と現在の状態を比較すると、
ロボットは「マンゴーを茶色の容器に入れる」というタスクを正常に実行しましたか？
（1）はいまたは（2）いいえでのみ回答してください。質問に最もよく答える番号（1）または（2）を返してください。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 22) より良い読み取りのために画像のセクションを拡大する

<img width="794" height="959" alt="image" src="https://github.com/user-attachments/assets/338a9381-8f71-4f61-a378-a57a9bc64f75" />

**プロンプト**

```
大気質測定値は何ですか？コード実行機能を使用して、画像を拡大して詳しく見てください。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 23) ロボットグリッパーとアイテムによるセグメンテーション

<img width="796" height="456" alt="image" src="https://github.com/user-attachments/assets/09403f45-f6e7-4fd1-8390-4614e3481e3a" />

**プロンプト**

```
この画像内の次のオブジェクトのセグメンテーションマスクを提供してください：マンゴー、左ロボットグリッパーフィンガー、右ロボットグリッパーフィンガー。
回答はJSON形式に従う必要があります：
[
  {
    "box_2d": [ymin, xmin, ymax, xmax],
    "label": "<オブジェクトのラベル>",
    "mask": "data:image/png;base64,<base64エンコードされたPNGマスク>"
  },
  ...
]
box_2d座標は0〜1000に正規化する必要があり、整数でなければなりません。
マスクは、ゼロ以外のピクセルがマスクを示すbase64エンコードされたPNG画像である必要があります。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 24) 関連オブジェクトの特定

<img width="793" height="504" alt="image" src="https://github.com/user-attachments/assets/69d4df26-a15d-4fcb-8ad7-d0b0cf9889b5" />

**プロンプト**

```
青いブロックとオレンジ色のボウルを見つけて指し示してください。返されるラベルは、検出されたオブジェクトを識別する名前である必要があります。
回答は、json形式に従う必要があります：[{"point": <point>, "label": <label1>}, ...]。
ポイントは、0-1000に正規化された[y, x]形式です。
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 25) キッチンシーン：**オブジェクトを指して名前を付ける**（2Dポイント）✅

![キッチンポインティング](https://storage.googleapis.com/gweb-developer-goog-blog-assets/images/unnamed-2_2.original.png)

**プロンプト**

```
画像内の次のアイテムを指し示してください：食器用洗剤、食器棚、蛇口、炊飯器、ユニコーン。
ポイントは、0-1000に正規化された[y, x]形式です。
実際に画像に存在するオブジェクトのみを含めてください。
戻り値：[{"point":[y,x],"label":"<name>"}]
```

ER 1.5は、正確で意味的に接地されたポイントを返します。モーションプランニングのために深度/姿勢スタックと組み合わせます。（[Google Developers Blog]）

---

### 26) ビデオでの時系列推論：**いつ何が起こったか？** 🧩

https://github.com/user-attachments/assets/5c55cbea-6946-4d8f-8e33-a17c4f0d6dd7

**プロンプト**

```
短いロボット操作ビデオが与えられます。
各ステップを正確なタイムスタンプとともにJSONで説明してください：
[{"start_timestamp":"MM:SS","end_timestamp":"MM:SS","description":"..."}]
```

ER 1.5は、**順序、因果関係、間隔**（例：「緑のマーカーをトレイに入れ、次に青/赤のペンをカップに入れる」）について推論でき、サブインターバルにズームインすることもできます。実行を検証し、エピソードに注釈を付けるために使用します。（[Google Developers Blog]）

---

### 27) コーヒーメーカー：**閉じる軌道を計画する**（多点パス）✅

![コーヒー蓋の軌道](https://storage.googleapis.com/gweb-developer-goog-blog-assets/images/image1_PCWZQMD.original.png)

**プロンプト**

```
今、コーヒーメーカーを閉じる必要があります。
ハンドルを閉じるための動きを示す8点の軌道をプロットしてください。
ハンドルから始めます。ポイントは[Y,X]で、0-1000に正規化されています。
戻り値：
[{"point":[Y,X],"label":"p0"}, {"point":[Y,X],"label":"p1"}, ...]
```

モーションプランナー（スムージング、衝突チェック）をシードするために出力を使用します。（[Google Developers Blog]）

---

### 28) コーヒーの片付け：**マグカップはどこに置くべきですか？**（アフォーダンスポインティング）✅

![マグカップを片付ける](https://storage.googleapis.com/gweb-developer-goog-blog-assets/images/image12_O0rW8PY.original.png)

**プロンプト**

```
コーヒーを飲み終えました。片付けるためにマグカップはどこに置くべきですか？
y/xを0-1000に正規化して[{"point":[y,x],"label":"<best place>"}]を返してください。
```

タスク完了後の**アフォーダンスを意識した配置**に最適です。（[Google Developers Blog]）

---

### 29) **地域のルールによるゴミの分別**（ツールの使用+空間的接地）🧩

https://github.com/user-attachments/assets/503b20d6-1995-4e71-9164-9a79d6bf6a4c

**プロンプト**

```
Google検索ツールを使用して、私の市の最新のリサイクル/堆肥化ルールを取得してください。
次に、この画像に基づいて、表示されている各アイテムを正しいゴミ箱に分別する方法を説明してください。
参照される各アイテムを、0-1000に正規化された[{"point":[y,x],"label":"<bin>"}]を使用して指し示してください。
VLAコントローラーに渡せるステップバイステップの計画を返してください。
```

ER 1.5は**ツールを呼び出し**（例：Google検索）、**「空間的に接地された」**計画のために**テキストとポイントを交互に**出力できます。（[Google Developers Blog]）

---

### 30) **参考写真から机を整理する**（長期計画）🧩

**プロンプト**

```
（A）現在の私の机の写真と（B）参考写真が与えられた場合、
ステップバイステップの再編成計画を作成してください。
各ステップには、0-1000に正規化されたターゲット配置を[{"box_2d":[ymin,xmin,ymax,xmax],"label":"<item>"}]として含め、
1〜2文の根拠を添えてください。
```

多くのステップにわたる**空間的接地**を伴う**エージェント的な計画**を示します。（[Google Developers Blog]）

---

### 31) **ラップトップ用のスペースを作る**（何を動かすかを選択する）🧩

![スペースを作る](https://ai.google.dev/gemini-api/docs/images/robotics/spatial-reasoning.png)

**プロンプト**

```
ラップトップ用のスペースを確保するために削除する必要がある単一のオブジェクトを指し示してください。
0-1000に正規化された[{"point":[y,x],"label":"<object to move>"}]を返してください。
```

ドキュメントの標準的な**オーケストレーション**の例—制約について推論し、最小限の変更を選択します。（[Google AI for Developers]）

---

### 32) **空間的に接地された指示で昼食を詰める**✅

![昼食を詰める](https://ai.google.dev/static/gemini-api/docs/images/robotics/packing-lunch.png)

**プロンプト**

```
この画像からランチボックスとランチバッグの詰め方を説明してください。
参照する各オブジェクトを指し示してください：
0-1000に正規化された[{"point":[y,x],"label":"<name>"}]。
番号付きのステップバイステップの計画を返してください。
```

レンダリングしたり、ピックアンドプレースコールに変換したりできる**多段階計画**とポイントを出力します。（[Google AI for Developers]）

---

### 33) **ロボットAPIを介したピックアンドプレース**（関数呼び出し）🧩

![ブロックとボウル](https://ai.google.dev/gemini-api/docs/images/robotics/robot-api-example.png)

**プロンプト**

```
まず、青いブロックとオレンジ色のボウルを次のように見つけます：
0-1000の[{"point":[y,x],"label":"<name>"}]。
次に、これらの関数のみを使用して：
  def move(x, y, high: bool)
  def setGripperState(opened: bool)
  def returnToOrigin()
ブロックを拾ってボウルに入れるための正確な呼び出しシーケンスを生成します。
各呼び出しを簡単に説明してください。
```

ドキュメントでは、このパターンをエンドツーエンドで示しています（検出→計画→APIの呼び出し）。（[Google AI for Developers]）

---

### 34) **赤いペンをオーガナイザーに移動する**（軌道計画）🧩

![ペンの軌道](https://ai.google.dev/gemini-api/docs/images/robotics/trajectories.png)

**プロンプト**

```
赤いペンに点を置き、次にその軌道をオーガナイザー上に15点置きます。
点を順に「0」...「15」とラベル付けします。[{"point":[y,x],"label":"<step>"}]を返してください。
```

**軌道による指導**や下流のスプラインフィッティング/時間パラメータ化に最適です。（[Google AI for Developers]）

---

### 35) **色による洗濯物の仕分け**（多段階思考）🧩

[![](https://i.imgur.com/2fqyZh6.png)](https://www.youtube.com/watch?v=eDyXEh8XqjM&t=34s)

**プロンプト**

```
洗濯物を色で2つのかごに仕分けます：白と白以外。
短い自然言語の計画と、各衣類の正規化された2D座標を持つ
{"point":[y,x],"label":"white|non-white"}を返してください。
```

公式ブログでは、Robotics 1.5/ER 1.5が**色による洗濯物の仕分け**などの家事のために、タスク/サブタスク/ミクロアクションレベルでどのように考えるかを説明しています。（[blog.google]）

---

### 36) **ペイロードを考慮したピック**（安全性と制約）🧩

**プロンプト**

```
1.0 kgのペイロード制限と25 cmのリーチ半径を条件として、
表示されている最も重いアイテムのピックアンドプレースを計画してください。
制約チェックを説明し、安全でない場合は拒否してください。
[{"point":[y,x],"label":"grasp"}]と「理由」を返してください。
```

ER 1.5は、意味的な安全性と物理的な制約の認識を向上させますが、ハードウェアレベルの安全性は依然として実装する必要があります。（[Google Developers Blog]）

---

### 37) **人間とロボットの自然言語**：「テーブルを片付けて」🧩

![テーブルの片付け](https://storage.googleapis.com/gweb-developer-goog-blog-assets/images/image12_O0rW8PY.original.png)

**プロンプト**

```
テーブルシーンの画像が与えられた場合、番号付きの片付け計画を作成してください。
各ステップには、ターゲット配置ポイントまたは2Dボックスと、成功基準に関するメモを含めてください。
フォーマット：
{"step":n,"action":"...","target":{"point":[y,x] | "box_2d":[ymin,xmin,ymax,xmax]},"why":"..."}
```

ER 1.5は、あいまいなコマンドを実行可能なサブ目標に分解し、特殊な把持/VLAスキルを呼び出して行動できます。（[Google Developers Blog]）

---

### 38) **クロスエンボディメント計画**（ロボットに依存しないステップ）🧩

[![エンボディメント](https://github.com/user-attachments/assets/919be470-810b-4974-8fe9-2784623aabd2)](https://www.youtube.com/watch?v=9FV5ZYytkOQ)

**プロンプト**

```
このワークステーションのシーンから、ロボットに依存しない計画を生成してください。
計画は、環境/アフォーダンスの用語のみで表現されます（関節空間なし）。
ALOHA 2、Apptronik Apollo、またはFrankaアームで実行されると仮定します。
成功チェックとフォールバックオプションを含むステップリストを返してください。
```

DeepMindは、スキル移転を高速化するために、エンボディメント間での一般化（例：ALOHA-2→Apollo/Franka）を強調しています。（[blog.google]）

---

## ヒントとパターン

*   ポイントには**0〜1000に正規化された[y,x]**、ボックスには`[ymin,xmin,ymax,xmax]`を使用することを推奨します。これにより、プロンプトがモデルフレンドリーで実装に依存しなくなります。（[Google Developers Blog]）
*   タスクの複雑さに応じて、**思考バジェット**（レイテンシー/精度のトレードオフ）を調整します。（[Google Developers Blog]）
*   **テキストとポイント/ボックス/軌跡**を交互に配置して、コントローラーが実行できる「空間的に接地された」計画を作成します。（[Google Developers Blog]）
*   **ツールの呼び出し**（例：検索）を使用して、地域のルール（リサイクル、キッチンポリシーなど）に計画を接地します。（[Google Developers Blog]）

---

## 貢献

*   `cases/<short-name>/`の下に新しいフォルダを追加します：

    *   `README.md`（1〜2文+プロンプト）
    *   `image.jpg/png`（またはリンク）
*   プロンプトは**コピーして実行可能**で**JSONフレンドリー**に保ちます。
*   出典を引用してください（主要なドキュメント/ブログ/ビデオを優先します）。

---

## ライセンスと画像の帰属

*   テキスト：MIT（このリポジトリ）。
*   ✅とマークされた画像はGoogleの公開ドキュメント/ブログからのものであり、ここでは**デモ参照**としてのみ使用されています。再配布する前にソースライセンスを確認してください。🧩プレースホルダーを独自の画像に置き換えてください。

**主要な情報源**

*   Google Developers Blog — *Gemini Robotics-ER 1.5による次世代の物理エージェントの構築*（公式プロンプト、画像、機能）。（[Google Developers Blog]）
*   Google AI for Developers — *Gemini Robotics-ER 1.5（ロボティクスの概要）*（ポイント、軌道、オーケストレーション、コード）。（[Google AI for Developers]）
*   Google / DeepMindブログ — ER 1.5のローンチコンテキスト、実体化された推論、クロスエンボディメント。日本のブログには、洗濯物の仕分けの例とパートナーロボットの参照が含まれています。（[Google DeepMind]）

---

> 💡 このファイルを
> `https://github.com/GitHub30/Awesome-Gemini-Robotics/blob/main/README.md`
> にドロップし、`assets/`の下に独自のスクリーンショットを追加して🧩プレースホルダーを置き換えることから始めます。

## 🙏 謝辞

このリポジトリのさまざまなケースは、AIコミュニティからの共有に依存しています。すべてのケース貢献者に心からの感謝を表明させていただきます。

素晴らしい作品を共有してくださった以下のユーザーに感謝します。彼らのプロフィールにアクセスして、さらに詳しく知ることもできます。

- [@GoogleDeepMind](https://x.com/GoogleDeepMind)
- [@GeminiApp](https://x.com/GeminiApp)

*すべてのケースが原作者からのものであるとは限りません。ご迷惑をおかけした場合は、お気軽にご連絡ください。修正いたします。*

私たちが収集したケースは、考えられるすべてのアプリケーションシナリオを網羅しているわけではありません。他に面白い発見🔍がありましたら、より多くの創造性を披露するためにご連絡いただくことを歓迎します📧！

[![Star History Chart](https://api.star-history.com/svg?repos=GitHub30/Awesome-Gemini-Robotics&type=Date)](https://www.star-history.com/#GitHub30/Awesome-Gemini-Robotics&Date)

: https://developers.googleblog.com/en/building-the-next-generation-of-physical-agents-with-gemini-robotics-er-15/ "Building the Next Generation of Physical Agents with Gemini Robotics-ER 1.5 - Google Developers Blog"
: https://ai.google.dev/gemini-api/docs/robotics-overview "Gemini Robotics-ER 1.5  |  Gemini API  |  Google AI for Developers"
: https://blog.google/intl/ja-jp/company-news/technology/gemini-robotics-15-ai/ "Gemini Robotics 1.5 を発表、AI エージェントを物理世界に"
: https://deepmind.google/discover/blog/gemini-robotics-15-brings-ai-agents-into-the-physical-world/ "Gemini Robotics 1.5 brings AI agents into the physical world - Google DeepMind"
