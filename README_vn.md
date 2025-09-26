<div align="center">
<img width="3334" height="992" alt="image" src="https://github.com/user-attachments/assets/32195746-89c3-4e33-a93d-00a7f0642f79" />

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
</div>

# Awesome Gemini Robotics

Một bộ sưu tập được tuyển chọn, duy trì bởi cộng đồng về các ví dụ và lời nhắc của **Gemini Robotics-ER 1.5** (suy luận cụ thể, VLM) mà bạn có thể sao chép và dán vào các dự án của riêng mình. Mỗi thẻ bao gồm một **hình ảnh đại diện** và một **lời nhắc sẵn sàng sử dụng** (thường thân thiện với JSON), theo phong cách của “Awesome Nano Banana Images”.
Nếu bạn xây dựng được thứ gì đó thú vị, hãy gửi một PR!

> **Gemini Robotics-ER 1.5 là gì?**
> Đây là mô hình suy luận cụ thể tiên tiến của Google dành cho robot. ER 1.5 vượt trội về **hiểu biết không gian (điểm 2D, hộp, quỹ đạo)**, **lập kế hoạch nhiệm vụ dài hạn**, **suy luận thời gian trên video**, **sử dụng công cụ (ví dụ: Google Search)** và **ước tính thành công/tiến độ**. Bạn có thể gọi nó trực tiếp qua Google AI Studio / Gemini API và điều phối các API robot hoặc VLA của riêng bạn. ([Google Developers Blog])

---

## Nội dung

* [Bắt đầu nhanh](#quick-start)
* [Các trường hợp sử dụng (10+)](#use-cases)
* [Đóng góp](#contributing)
* [Giấy phép & ghi công hình ảnh](#license--image-attribution)

---

## Bắt đầu nhanh

> Bản phác thảo Python tối thiểu với API Gemini (tên mô hình có thể xuất hiện dưới dạng `gemini-robotics-er-1.5-preview` trong quyền truy cập sớm):

```python
from google import genai
from google.genai import types

client = genai.Client()
MODEL_ID = "gemini-robotics-er-1.5-preview"

prompt = """
Chỉ vào không quá 10 mục trong hình ảnh.
Trả về [{"point":[y,x],"label":"<name>"}] với y/x được chuẩn hóa thành 0-1000.
"""

img_bytes = open("scene.jpg","rb").read()
res = client.models.generate_content(
    model=MODEL_ID,
    contents=[types.Part.from_bytes(data=img_bytes, mime_type="image/jpeg"), prompt],
    config=types.GenerateContentConfig(
        temperature=0.5,
        thinking_config=types.ThinkingConfig(thinking_budget=0)  # điều chỉnh nếu cần
    )
)
print(res.text)
```

Điều này phản ánh tổng quan về robot chính thức của Google (điểm, quỹ đạo, điều phối, video, v.v.). Xem tài liệu để biết thêm ví dụ (theo dõi đối tượng, đóng gói bữa trưa, gọi API robot tùy chỉnh). ([Google AI for Developers])

---

## Các trường hợp sử dụng

> ✅ = bao gồm hình ảnh demo công khai từ tài liệu/blog của Google • 🧩 = mang theo hình ảnh của riêng bạn (đường dẫn giữ chỗ)

### 1) Chỉ vào các đối tượng không xác định

<img width="801" height="598" alt="image" src="https://github.com/user-attachments/assets/35881c79-922e-43dd-9d1c-b22e4f66715d" />

**Lời nhắc**

```
Chỉ vào không quá 10 mục trong hình ảnh. Nhãn được trả về phải là tên nhận dạng cho đối tượng được phát hiện.
Câu trả lời phải tuân theo định dạng json: [{"point": <point>, "label": <label1>}, ...]. Các điểm ở định dạng [y, x] được chuẩn hóa thành 0-1000.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 2) Chỉ vào các đối tượng đã xác định

<img width="800" height="601" alt="image" src="https://github.com/user-attachments/assets/8bf517fa-a094-40c9-be65-6c7bedfde295" />

**Lời nhắc**

```
Lấy tất cả các điểm khớp với các đối tượng sau: bánh mì, khế, chuối. Nhãn được trả về phải là tên nhận dạng cho đối tượng được phát hiện.
Câu trả lời phải tuân theo định dạng json: [{"point": <point>, "label": <label1>}, ...]. Các điểm ở định dạng [y, x] được chuẩn hóa thành 0-1000.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 3) Chỉ vào tất cả các trường hợp của một đối tượng dựa trên mô tả trừu tượng hơn (ví dụ: "trái cây")

<img width="798" height="597" alt="image" src="https://github.com/user-attachments/assets/974995c1-9a92-4c04-9e89-3bcbefdd22d8" />

**Lời nhắc**

```
Lấy tất cả các điểm cho trái cây. Nhãn được trả về phải là tên nhận dạng cho đối tượng được phát hiện.
Câu trả lời phải tuân theo định dạng json: [{"point": <point>, "label": <label1>}, ...]. Các điểm ở định dạng [y, x] được chuẩn hóa thành 0-1000.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 4) Chỉ vào tất cả các trường hợp của một đối tượng

<img width="799" height="450" alt="image" src="https://github.com/user-attachments/assets/93382594-8156-43ff-9864-aa627346b2ae" />

**Lời nhắc**

```
Lấy tất cả các điểm khớp với khe cắm bảng trò chơi. Nhãn được trả về phải là tên nhận dạng cho đối tượng được phát hiện.
Câu trả lời phải tuân theo định dạng json: [{"point": <point>, "label": <label1>}, ...]. Các điểm ở định dạng [y, x] được chuẩn hóa thành 0-1000.
```

```
Lấy tất cả các điểm khớp với quân cờ X. Nhãn được trả về phải là tên nhận dạng cho đối tượng được phát hiện.
Câu trả lời phải tuân theo định dạng json: [{"point": <point>, "label": <label1>}, ...]. Các điểm ở định dạng [y, x] được chuẩn hóa thành 0-1000.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 5) Chỉ vào các bộ phận nhất định của một đối tượng theo chuỗi

<img width="795" height="592" alt="image" src="https://github.com/user-attachments/assets/68c6bd2f-a064-43e4-bee3-a377a33d73c7" />

**Lời nhắc**

```
queries = [
    ("túi giấy", "quai"),
    ("chuối", "cuống"),
    ("chuối", "trung tâm"),
    ("khế", "trung tâm"),
    ("chanh", "trung tâm"),
    ("bát màu xanh nhạt", "vành"),
    ("bát màu xanh đậm", "vành"),
    ("cốc đo", "vành"),
    ("cốc đo", "tay cầm"),
    ("bát", "cà chua"),
]

Chỉ vào $part của $object trong hình ảnh. Trả về câu trả lời dưới dạng danh sách json của một từ điển có các khóa "point" và "label". Chỉ trả về một điểm cho yêu cầu này
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 6) Đếm bằng cách chỉ

<img width="793" height="820" alt="image" src="https://github.com/user-attachments/assets/2592ca4e-c1a9-4510-a251-c63dbbeaa52d" />

**Lời nhắc**

```
Chỉ vào mỗi vòng đệm trong hộp.
Trả về câu trả lời ở định dạng: [{"point": <point>, "label": <label1>}, ...]. Các điểm ở định dạng [y, x] được chuẩn hóa thành 0-1000.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 7) Chỉ vào các đối tượng đã xác định trong ảnh GIF

![tải xuống (1)](https://github.com/user-attachments/assets/635a748f-eae9-44c3-88b8-31627eec0ed8)

**Lời nhắc**

```
queries = [
    "bút (trên bàn)",
    "bút (trong tay robot)",
    "máy tính xách tay (mở)",
    "máy tính xách tay (đóng)",
]

Chỉ vào các đối tượng sau trong hình ảnh được cung cấp: {', '.join(queries)}.
Câu trả lời phải tuân theo định dạng json: [{"point": <point>, "label": <label1>}, ...].
Các điểm ở định dạng [y, x] được chuẩn hóa thành 0-1000.
Nếu không tìm thấy đối tượng nào, hãy trả về một danh sách JSON trống [].
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 8) Hộp giới hạn 2D

<img width="794" height="597" alt="image" src="https://github.com/user-attachments/assets/afaa8594-e682-4aaa-aa4c-62c468a66e49" />


**Lời nhắc**

```
Trả về các hộp giới hạn dưới dạng một mảng JSON có nhãn. Không bao giờ trả về mặt nạ hoặc hàng rào mã. Giới hạn ở 25 đối tượng. Bao gồm càng nhiều đối tượng càng tốt mà bạn có thể xác định trên bàn.
Nếu một đối tượng xuất hiện nhiều lần, hãy đặt tên cho chúng theo đặc điểm riêng của chúng (màu sắc, kích thước, vị trí, đặc điểm riêng, v.v.).
Định dạng phải như sau: [{"box_2d": [ymin, xmin, ymax, xmax], "label": <label for the object>}] được chuẩn hóa thành 0-1000. Các giá trị trong box_2d chỉ được là số nguyên
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 9) Lập kế hoạch quỹ đạo đơn giản

<img width="794" height="444" alt="image" src="https://github.com/user-attachments/assets/5c2f94dc-9b08-4e2b-b1d3-58ca55c6fb51" />

**Lời nhắc**

```
Đặt một điểm trên bút màu đỏ, sau đó 15 điểm cho quỹ đạo di chuyển bút màu đỏ lên đầu của bộ sắp xếp ở bên trái.
Các điểm phải được gắn nhãn theo thứ tự của quỹ đạo, từ '0' (điểm bắt đầu ở tay trái) đến <n> (điểm cuối cùng)
Câu trả lời phải tuân theo định dạng json: [{"point": <point>, "label": <label1>}, ...].
Các điểm ở định dạng [y, x] được chuẩn hóa thành 0-1000.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 10) Đường đi để chải các hạt

<img width="792" height="592" alt="image" src="https://github.com/user-attachments/assets/855c0a41-29f7-4d3b-a774-c22e4d726ac4" />

**Lời nhắc**

```
Chỉ vào bàn chải màu xanh và danh sách 10 điểm bao phủ vùng hạt.
Đảm bảo rằng các điểm được trải đều trên các hạt để tạo ra một quỹ đạo trơn tru.
Gắn nhãn các điểm từ 1 đến 10 dựa trên thứ tự chúng nên được tiếp cận trong quỹ đạo làm sạch đĩa.
Chuyển động phải bắt đầu từ bàn chải.
Câu trả lời phải tuân theo định dạng json: [{"point": <point>, "label": <label1>}, ...].
Các điểm ở định dạng [y, x] được chuẩn hóa thành 0-1000.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 11) Lập kế hoạch quỹ đạo tránh chướng ngại vật

<img width="801" height="743" alt="image" src="https://github.com/user-attachments/assets/53cfab6e-db07-4eb7-b325-27d60da55eee" />

**Lời nhắc**

```
Tìm quỹ đạo không va chạm trực tiếp nhất gồm 10 điểm trên sàn giữa gốc xem hiện tại và ghế đẩu màu xanh lá cây ở phía sau bên trái. Các điểm phải tránh tất cả các chướng ngại vật khác trên sàn.
Câu trả lời phải tuân theo định dạng json: [{"point": <point>, "label": <label1>}, ...].
Các điểm ở định dạng [y, x] được chuẩn hóa thành 0-1000.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 12) Vật phẩm cần loại bỏ để nhường chỗ cho máy tính xách tay

<img width="795" height="598" alt="image" src="https://github.com/user-attachments/assets/70a17400-d409-4ec9-8c23-4a2595f528ec" />

**Lời nhắc**

```
Chỉ vào đối tượng mà tôi cần loại bỏ để nhường chỗ cho máy tính xách tay của mình
Câu trả lời phải tuân theo định dạng json: [{"point": <point>, "label": <label1>}, ...]. Các điểm ở định dạng [y, x] được chuẩn hóa thành 0-1000.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 13) Điều phối: Đóng gói bữa trưa

<img width="1207" height="855" alt="image" src="https://github.com/user-attachments/assets/aab2f569-99fe-4d7e-ae34-159655729a59" />

**Lời nhắc**

```
Giải thích cách đóng gói hộp cơm và túi cơm. Chỉ vào từng đối tượng mà bạn đề cập đến. Mỗi điểm phải ở định dạng: [{"point": [y, x], "label": }], trong đó tọa độ được chuẩn hóa trong khoảng 0-1000.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 14) Ổ cắm điện trống

<img width="792" height="938" alt="image" src="https://github.com/user-attachments/assets/469d73df-3b01-48d6-b1f5-e39b62aa5133" />

**Lời nhắc**

```
Chỉ vào các ổ cắm trống không bị cản trở
Câu trả lời phải tuân theo định dạng json: [{"point": <point>, "label": <label1>}, ...]. Các điểm ở định dạng [y, x] được chuẩn hóa thành 0-1000.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 15) Hạn chế nâng vật phẩm (giới hạn 3LB)

<img width="792" height="954" alt="image" src="https://github.com/user-attachments/assets/a45216e1-cac7-4217-b51e-98d0d23e8754" />

**Lời nhắc**

```
Tôi là một robot có tải trọng 3LBs. Chỉ vào tất cả các đối tượng trong hình ảnh mà tôi có thể nhặt được.
Câu trả lời phải tuân theo định dạng json: [{"point": <point>, "label": <label1>}, ...]. Các điểm ở định dạng [y, x] được chuẩn hóa thành 0-1000.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 16) Phân tích video

https://github.com/user-attachments/assets/cc2b3c62-2ce6-4c7b-bcf8-968f000e37f5

**Lời nhắc**

```
Mô tả chi tiết từng bước hoàn thành nhiệm vụ. Phân tích theo dấu thời gian, đầu ra ở định dạng json với các khóa "start_timestamp", "end_timestamp" và "description".
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 17) Phân tích video: Phạm vi thời gian

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
phóng to từ giây 15 đến 22 và cung cấp phân tích chi tiết theo từng giây về những gì đang xảy ra ở cùng một định dạng.
"""

start_time = time.time()

response = chat.send_message(
    [prompt, myfile]
)

end_time = time.time()
elapsed_time = end_time - start_time
print(f"\nTổng thời gian xử lý: {elapsed_time:.4f} giây")

print(response.text)
```

Tổng thời gian xử lý: 11.4140 giây
```json
[
  {
    "start_timestamp": "00:15",
    "end_timestamp": "00:16",
    "description": "Cánh tay robot bên trái bắt đầu di chuyển từ vị trí phía trên bàn về phía cây bút màu xanh."
  },
  {
    "start_timestamp": "00:16",
    "end_timestamp": "00:18",
    "description": "Cánh tay robot bên trái định vị bộ kẹp của nó ngay trên cây bút màu xanh trên bàn."
  },
  {
    "start_timestamp": "00:18",
    "end_timestamp": "00:19",
    "description": "Cánh tay robot bên trái hạ thấp bộ kẹp và nắm lấy cây bút màu xanh."
  },
  {
    "start_timestamp": "00:19",
    "end_timestamp": "00:20",
    "description": "Cánh tay robot bên trái nhấc cây bút màu xanh lên khỏi bề mặt bàn."
  },
  {
    "start_timestamp": "00:20",
    "end_timestamp": "00:21",
    "description": "Cánh tay robot bên trái di chuyển cây bút màu xanh về phía trung tâm của bàn, định vị nó phía trên hộp đựng bút lưới màu đen."
  },
  {
    "start_timestamp": "00:21",
    "end_timestamp": "00:22",
    "description": "Cánh tay robot bên trái thả cây bút màu xanh, thả nó vào hộp đựng bút lưới màu đen."
  }
]
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 18) Tìm hàng kệ thứ tư

<img width="794" height="922" alt="image" src="https://github.com/user-attachments/assets/f7d26839-67a8-4215-b23d-e3eeb89ff11e" />

**Lời nhắc**

```
Trả về các hộp giới hạn dưới dạng một mảng JSON có nhãn làm nổi bật tất cả các ngăn trong hàng kệ thứ tư.
Định dạng phải như sau: [{"box_2d": [ymin, xmin, ymax, xmax], "label": <label for the object>}] được chuẩn hóa thành 0-1000. Các giá trị trong box_2d chỉ được là số nguyên
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 19) Tìm kệ có các mặt hàng cụ thể

<img width="794" height="942" alt="image" src="https://github.com/user-attachments/assets/95baca11-e7aa-485c-a927-0e392d9f6390" />

**Lời nhắc**

```
"Tôi cần xì mũi."
Tìm ngăn có thể giúp.
Định dạng phải như sau: [{"box_2d": [ymin, xmin, ymax, xmax], "label": <label for the object>}] được chuẩn hóa thành 0-1000. Các giá trị trong box_2d chỉ được là số nguyên
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 20) Đếm các mục bằng cách suy nghĩ

<img width="960" height="600" alt="image" src="https://github.com/user-attachments/assets/d902b60a-006e-406a-8641-611240dd9f87" />

**Đầu ra**

```
Dựa trên kiểm tra trực quan giỏ hàng ở phía trước hình ảnh, có **8 mặt hàng** bên trong.

Đây là lý do cho việc đếm:

1.  **Các hộp xếp chồng:** Có một chồng gồm nhiều hộp ở phía bên trái của giỏ. Chúng ta có thể xác định rõ ràng ít nhất năm hộp trong chồng này:
    *   Một hộp mì Barilla màu xanh ở phía dưới bên trái.
    *   Một hộp Wheat Thins màu vàng bên cạnh hộp Barilla.
    *   Ít nhất ba hộp nữa xếp chồng lên trên hai hộp này, có thể nhìn thấy một phần.
2.  **Lon:** Có hai lon bạc có thể nhìn thấy ở phía dưới bên phải của giỏ.
3.  **Gói:** Có một gói nhỏ màu xanh nằm ở trung tâm của giỏ, giữa các hộp và lon.
```

**Lời nhắc**

```
Có bao nhiêu mặt hàng bên trong giỏ hàng. Vui lòng chia sẻ lý do của bạn.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 21) Tìm kệ có các mặt hàng cụ thể

| ![initial_state_1](https://storage.googleapis.com/generativeai-downloads/images/robotics/er-1-5-example-colab/initial_state_1.png) | ![initial_state_2](https://storage.googleapis.com/generativeai-downloads/images/robotics/er-1-5-example-colab/initial_state_2.png) | ![initial_state_3](https://storage.googleapis.com/generativeai-downloads/images/robotics/er-1-5-example-colab/initial_state_3.png) | ![initial_state_4](https://storage.googleapis.com/generativeai-downloads/images/robotics/er-1-5-example-co|---|---|---|---|
| ![current_state_1](https://storage.googleapis.com/generativeai-downloads/images/robotics/er-1-5-example-colab/current_state_1.png) | ![current_state_2](https://storage.googleapis.com/generativeai-downloads/images/robotics/er-1-5-example-colab/current_state_2.png) | ![current_state_3](https://storage.googleapis.com/generativeai-downloads/images/robotics/er-1-5-example-colab/current_state_3.png) | ![current_state_4](https://storage.googleapis.com/generativeai-downloads/images/robotics/er-1-5-example-colab/current_state_4.png) |


**Đầu ra**

Thành công? Không

**Lời nhắc**

```
Đối với nhiệm vụ này, bạn sẽ thấy một robot hoặc con người đang cố gắng thực hiện nhiệm vụ đặt quả xoài vào thùng chứa màu nâu.
Bạn có thể thấy nhiều chế độ xem camera của cùng một cảnh. Một số camera tĩnh
và được gắn bên ngoài cảnh và một số camera được gắn trên cánh tay robot
và do đó chúng đang di chuyển trong suốt tập phim. 4 hình ảnh đầu tiên cho thấy nhiều
chế độ xem camera từ đầu tập phim (một thời gian trước). 4 hình ảnh cuối cùng cho thấy
nhiều chế độ xem camera từ thời điểm hiện tại trong tập phim (như bây giờ).
Nhìn vào những hình ảnh này và so sánh phần đầu của tập phim với trạng thái hiện tại đã
robot thực hiện thành công nhiệm vụ "đặt quả xoài vào thùng chứa màu nâu"?
Chỉ trả lời bằng (1) có hoặc (2) không. Trả về số (1) hoặc (2) trả lời đúng nhất câu hỏi.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 22) Phóng to các phần của hình ảnh để đọc tốt hơn

<img width="794" height="959" alt="image" src="https://github.com/user-attachments/assets/338a9381-8f71-4f61-a378-a57a9bc64f75" />

**Lời nhắc**

```
Chỉ số chất lượng không khí là bao nhiêu? Sử dụng tính năng thực thi mã, phóng to hình ảnh để xem xét kỹ hơn.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 23) Phân đoạn bằng kẹp robot và vật phẩm

<img width="796" height="456" alt="image" src="https://github.com/user-attachments/assets/09403f45-f6e7-4fd1-8390-4614e3481e3a" />

**Lời nhắc**

```
Cung cấp mặt nạ phân đoạn cho các đối tượng sau trong hình ảnh này: xoài, ngón kẹp robot trái, ngón kẹp robot phải.
Câu trả lời phải tuân theo định dạng JSON:
[
  {
    "box_2d": [ymin, xmin, ymax, xmax],
    "label": "<label for the object>",
    "mask": "data:image/png;base64,<base64 encoded PNG mask>"
  },
  ...
]
Tọa độ box_2d phải được chuẩn hóa thành 0-1000 và phải là số nguyên.
Mặt nạ phải là hình ảnh PNG được mã hóa base64 trong đó các pixel khác không cho biết mặt nạ.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 24) Xác định vị trí các đối tượng có liên quan

<img width="793" height="504" alt="image" src="https://github.com/user-attachments/assets/69d4df26-a15d-4fcb-8ad7-d0b0cf9889b5" />

**Lời nhắc**

```
Xác định vị trí và chỉ vào khối màu xanh và bát màu cam. Nhãn
được trả về phải là tên nhận dạng cho đối tượng được phát hiện.
Câu trả lời phải tuân theo định dạng json: [{"point": <point>, "label": <label1>}, ...].
Các điểm ở định dạng [y, x] được chuẩn hóa thành 0-1000.
```

https://github.com/google-gemini/cookbook/blob/main/quickstarts/gemini-robotics-er.ipynb

### 25) Cảnh nhà bếp: **Chỉ và đặt tên các đối tượng** (điểm 2D) ✅

![Chỉ nhà bếp](https://storage.googleapis.com/gweb-developer-goog-blog-assets/images/unnamed-2_2.original.png)

**Lời nhắc**

```
Chỉ vào các mục sau trong hình ảnh: xà phòng rửa chén, giá để chén, vòi nước, nồi cơm điện, kỳ lân.
Các điểm ở định dạng [y, x] được chuẩn hóa thành 0-1000.
Chỉ bao gồm các đối tượng thực sự có trong hình ảnh.
Trả về: [{"point":[y,x],"label":"<name>"}]
```

ER 1.5 trả về các điểm chính xác, có cơ sở ngữ nghĩa; kết hợp với ngăn xếp độ sâu/tư thế của bạn để lập kế hoạch chuyển động. ([Google Developers Blog])

---

### 26) Suy luận thời gian trên video: **Chuyện gì đã xảy ra khi nào?** 🧩




https://github.com/user-attachments/assets/5c55cbea-6946-4d8f-8e33-a17c4f0d6dd7




**Lời nhắc**

```
Bạn được cung cấp một video thao tác robot ngắn.
Mô tả từng bước với dấu thời gian chính xác, dưới dạng JSON:
[{"start_timestamp":"MM:SS","end_timestamp":"MM:SS","description":"..."}]
```

ER 1.5 có thể suy luận về **thứ tự, quan hệ nhân quả và khoảng thời gian** (ví dụ: “đánh dấu màu xanh lá cây vào khay, sau đó bút màu xanh/đỏ vào cốc”), và thậm chí phóng to vào các khoảng thời gian phụ. Sử dụng nó để xác minh việc thực thi và chú thích các tập phim. ([Google Developers Blog])

---

### 27) Máy pha cà phê: **Lập kế hoạch quỹ đạo đóng** (đường đi nhiều điểm) ✅

![Quỹ đạo nắp cà phê](https://storage.googleapis.com/gweb-developer-goog-blog-assets/images/image1_PCWZQMD.original.png)

**Lời nhắc**

```
Bây giờ tôi cần đóng máy pha cà phê.
Vẽ một quỹ đạo gồm 8 điểm cho biết tay cầm nên di chuyển như thế nào để đóng nó.
Bắt đầu từ tay cầm. Các điểm là [Y,X], được chuẩn hóa 0-1000.
Trả về:
[{"point":[Y,X],"label":"p0"}, {"point":[Y,X],"label":"p1"}, ...]
```

Sử dụng các đầu ra để tạo hạt cho bộ lập kế hoạch chuyển động của bạn (làm mịn, kiểm tra va chạm). ([Google Developers Blog])

---

### 28) Dọn dẹp cà phê: **Tôi nên đặt cốc ở đâu?** (chỉ khả năng) ✅

![Đặt cốc đi](https://storage.googleapis.com/gweb-developer-goog-blog-assets/images/image12_O0rW8PY.original.png)

**Lời nhắc**

```
Tôi đã uống xong cà phê. Bây giờ tôi nên đặt cốc ở đâu để dọn dẹp?
Trả về [{"point":[y,x],"label":"<best place>"}] với y/x được chuẩn hóa 0-1000.
```

Tuyệt vời cho **vị trí nhận biết khả năng** sau khi hoàn thành nhiệm vụ. ([Google Developers Blog])

---

### 29) **Phân loại rác theo quy tắc địa phương** (sử dụng công cụ + tiếp đất không gian) 🧩

https://github.com/user-attachments/assets/503b20d6-1995-4e71-9164-9a79d6bf6a4c

**Lời nhắc**

```
Sử dụng công cụ Google Search để tìm các quy tắc tái chế/phân hủy mới nhất của thành phố tôi.
Sau đó, dựa trên hình ảnh này, giải thích cách phân loại từng mục có thể nhìn thấy vào đúng thùng.
Chỉ vào từng mục được tham chiếu bằng cách sử dụng [{"point":[y,x],"label":"<bin>"}], được chuẩn hóa 0-1000.
Trả về một kế hoạch từng bước mà tôi có thể giao cho bộ điều khiển VLA của mình.
```

ER 1.5 có thể **gọi các công cụ** (ví dụ: Google Search) và đầu ra **văn bản + điểm xen kẽ** cho các kế hoạch “có cơ sở không gian”. ([Google Developers Blog])

---

### 30) **Sắp xếp lại bàn của tôi** từ một bức ảnh tham khảo (kế hoạch dài hạn) 🧩

**Lời nhắc**

```
Cho (A) ảnh bàn hiện tại của tôi và (B) ảnh tham khảo,
tạo một kế hoạch sắp xếp lại từng bước.
Đối với mỗi bước, bao gồm các vị trí mục tiêu dưới dạng [{"box_2d":[ymin,xmin,ymax,xmax],"label":"<item>"}],
được chuẩn hóa 0-1000 và một lý do dài 1-2 câu.
```

Thể hiện **lập kế hoạch tác nhân** với **tiếp đất không gian** qua nhiều bước. ([Google Developers Blog])

---

### 31) **Nhường chỗ cho máy tính xách tay** (chọn thứ cần di chuyển) 🧩

![Nhường chỗ](https://ai.google.dev/gemini-api/docs/images/robotics/spatial-reasoning.png)

**Lời nhắc**

```
Chỉ vào một đối tượng duy nhất mà tôi nên loại bỏ để nhường chỗ cho máy tính xách tay của mình.
Trả về [{"point":[y,x],"label":"<object to move>"}], được chuẩn hóa 0-1000.
```

Một ví dụ **điều phối** kinh điển từ tài liệu—suy luận về các ràng buộc và chọn thay đổi tối thiểu. ([Google AI for Developers])

---

### 32) **Đóng gói bữa trưa** với các hướng dẫn có cơ sở không gian ✅

![Đóng gói bữa trưa](https://ai.google.dev/static/gemini-api/docs/images/robotics/packing-lunch.png)

**Lời nhắc**

```
Giải thích cách đóng gói hộp cơm và túi cơm từ hình ảnh này.
Chỉ vào từng đối tượng mà bạn đề cập đến:
[{"point":[y,x],"label":"<name>"}], được chuẩn hóa 0-1000.
Trả về một kế hoạch từng bước được đánh số.
```

Xuất ra một **kế hoạch nhiều bước** cộng với các điểm bạn có thể kết xuất hoặc chuyển đổi thành các lệnh gọi nhặt và đặt. ([Google AI for Developers])

---

### 33) **Nhặt và đặt qua API robot của bạn** (gọi hàm) 🧩

![Khối & bát](https://ai.google.dev/gemini-api/docs/images/robotics/robot-api-example.png)

**Lời nhắc**

```
Đầu tiên, xác định vị trí khối màu xanh và bát màu cam với:
[{"point":[y,x],"label":"<name>"}], 0-1000.
Sau đó, chỉ sử dụng các hàm sau:
  def move(x, y, high: bool)
  def setGripperState(opened: bool)
  def returnToOrigin()
Tạo chuỗi lệnh gọi chính xác để nhặt khối và đặt nó vào bát.
Giải thích ngắn gọn từng lệnh gọi.
```

Tài liệu cho thấy mẫu này từ đầu đến cuối (phát hiện → lập kế hoạch → gọi API của bạn). ([Google AI for Developers])

---

### 34) **Di chuyển bút màu đỏ đến bộ sắp xếp** (lập kế hoạch quỹ đạo) 🧩

![Quỹ đạo bút](https://ai.google.dev/gemini-api/docs/images/robotics/trajectories.png)

**Lời nhắc**

```
Đặt một điểm trên bút màu đỏ, sau đó 15 điểm cho quỹ đạo của nó lên bộ sắp xếp.
Gắn nhãn các điểm theo thứ tự "0"..."15". Trả về [{"point":[y,x],"label":"<step>"}].
```

Tuyệt vời để **dạy theo quỹ đạo** và trang bị spline xuôi dòng / tham số hóa thời gian. ([Google AI for Developers])

---

### 35) **Phân loại đồ giặt theo màu sắc** (suy nghĩ đa cấp) 🧩

[![](https://i.imgur.com/2fqyZh6.png)](https://www.youtube.com/watch?v=eDyXEh8XqjM&t=34s)


**Lời nhắc**

```
Phân loại đồ giặt theo màu sắc thành hai giỏ: trắng và không trắng.
Trả về một kế hoạch ngôn ngữ tự nhiên ngắn và cho mỗi loại quần áo một
{"point":[y,x],"label":"white|non-white"} với tọa độ 2D được chuẩn hóa.
```

Blog chính thức giải thích cách Robotics 1.5/ER 1.5 suy nghĩ ở các cấp độ nhiệm vụ/nhiệm vụ phụ/hành động vi mô cho các công việc vặt như **phân loại đồ giặt theo màu sắc**. ([blog.google])

---

### 36) **Nhặt hàng có nhận biết tải trọng** (an toàn & ràng buộc) 🧩


**Lời nhắc**

```
Lập kế hoạch nhặt và đặt cho vật nặng nhất có thể nhìn thấy
tuân theo giới hạn tải trọng 1.0 kg và bán kính tiếp cận 25 cm.
Giải thích các kiểm tra ràng buộc và từ chối nếu không an toàn.
Trả về [{"point":[y,x],"label":"grasp"}] cộng với "reason".
```

ER 1.5 bổ sung độ an toàn ngữ nghĩa được cải thiện và nhận thức về các ràng buộc vật lý; bạn vẫn phải triển khai an toàn ở cấp độ phần cứng. ([Google Developers Blog])

---

### 37) **Ngôn ngữ tự nhiên người-robot**: “Dọn dẹp bàn” 🧩

![Dọn dẹp bàn](https://storage.googleapis.com/gweb-developer-goog-blog-assets/images/image12_O0rW8PY.original.png)

**Lời nhắc**

```
Cho một hình ảnh cảnh bàn, tạo ra một kế hoạch dọn dẹp được đánh số.
Đối với mỗi bước, bao gồm một điểm vị trí mục tiêu hoặc hộp 2D và một ghi chú về tiêu chí thành công.
Định dạng:
{"step":n,"action":"...","target":{"point":[y,x] | "box_2d":[ymin,xmin,ymax,xmax]},"why":"..."}
```

ER 1.5 chia nhỏ các lệnh mơ hồ thành các mục tiêu phụ có thể thực thi và có thể gọi các kỹ năng nắm bắt/VLA chuyên biệt để hành động. ([Google Developers Blog])

---

### 38) **Lập kế hoạch đa hiện thân** (các bước bất khả tri về robot) 🧩

[![Các hiện thân](https://github.com/user-attachments/assets/919be470-810b-4974-8fe9-2784623aabd2)](https://www.youtube.com/watch?v=9FV5ZYytkOQ)

**Lời nhắc**

```
Từ cảnh trạm làm việc này, tạo ra một kế hoạch bất khả tri về robot
chỉ được thể hiện bằng các thuật ngữ môi trường/khả năng (không có không gian khớp).
Giả sử nó sẽ được thực thi trên ALOHA 2, Apptronik Apollo hoặc một cánh tay Franka.
Trả về một danh sách các bước với các kiểm tra thành công và các tùy chọn dự phòng.
```

DeepMind nhấn mạnh sự khái quát hóa trên các hiện thân (ví dụ: ALOHA-2 → Apollo/Franka) để tăng tốc độ chuyển giao kỹ năng. ([blog.google])

---

## Mẹo & mẫu

* Ưu tiên **[y,x] được chuẩn hóa trong 0–1000** cho các điểm, hoặc `[ymin,xmin,ymax,xmax]` cho các hộp. Điều này giữ cho các lời nhắc thân thiện với mô hình và bất khả tri về việc triển khai. ([Google Developers Blog])
* Điều chỉnh **ngân sách suy nghĩ** (độ trễ/độ chính xác đánh đổi) tùy thuộc vào độ phức tạp của nhiệm vụ. ([Google Developers Blog])
* Xen kẽ **văn bản + điểm/hộp/quỹ đạo** để tạo ra các kế hoạch “có cơ sở không gian” mà bộ điều khiển của bạn có thể thực thi. ([Google Developers Blog])
* Sử dụng **lệnh gọi công cụ** (ví dụ: Search) để đưa các kế hoạch vào các quy tắc địa phương (tái chế, chính sách nhà bếp, v.v.). ([Google Developers Blog])

---

## Đóng góp

* Thêm một thư mục mới trong `cases/<short-name>/` với:

  * `README.md` (1–2 câu + lời nhắc)
  * `image.jpg/png` (hoặc liên kết)
* Giữ các lời nhắc **có thể sao chép và chạy** và **thân thiện với JSON**.
* Trích dẫn (các) nguồn của bạn (ưu tiên các tài liệu/blog/video chính).

---

## Giấy phép & ghi công hình ảnh

* Văn bản: MIT (repo này).
* Các hình ảnh được đánh dấu ✅ là từ các tài liệu/blog công khai của Google và chỉ được sử dụng ở đây làm **tham khảo demo**; vui lòng kiểm tra giấy phép nguồn trước khi phân phối lại. Thay thế các trình giữ chỗ 🧩 bằng hình ảnh của riêng bạn.

**Nguồn chính**

* Blog của nhà phát triển Google — *Xây dựng thế hệ tiếp theo của các tác nhân vật lý với Gemini Robotics-ER 1.5* (lời nhắc, hình ảnh, khả năng chính thức). ([Google Developers Blog])
* Google AI cho nhà phát triển — *Gemini Robotics-ER 1.5 (Tổng quan về robot)* (điểm, quỹ đạo, điều phối, mã). ([Google AI for Developers])
* Các blog của Google / DeepMind — Bối cảnh ra mắt ER 1.5, suy luận cụ thể, đa hiện thân; Blog JP bao gồm ví dụ phân loại đồ giặt và tài liệu tham khảo về robot đối tác. ([Google DeepMind])

---

> 💡 Thả tệp này vào
> `https://github.com/GitHub30/Awesome-Gemini-Robotics/blob/main/README.md`
> và bắt đầu thêm ảnh chụp màn hình của riêng bạn trong `assets/` để thay thế các trình giữ chỗ 🧩.

## 🙏 Ghi nhận

Các trường hợp khác nhau trong kho lưu trữ này dựa trên sự chia sẻ từ cộng đồng AI. Xin cho phép chúng tôi bày tỏ lòng biết ơn chân thành đến tất cả những người đóng góp cho các trường hợp.

Cảm ơn những người dùng sau đã chia sẻ những tác phẩm tuyệt vời của họ. Bạn cũng có thể truy cập hồ sơ của họ để tìm hiểu thêm:

- [@GoogleDeepMind](https://x.com/GoogleDeepMind)
- [@GeminiApp](https://x.com/GeminiApp)

*Chúng tôi không thể đảm bảo rằng tất cả các trường hợp đều đến từ tác giả gốc. Nếu điều này gây ra cho bạn bất kỳ sự bất tiện nào, vui lòng liên hệ với chúng tôi để sửa đổi.*

Các trường hợp chúng tôi thu thập không thể bao gồm tất cả các tình huống ứng dụng có thể có. Nếu bạn có những khám phá thú vị khác 🔍, chúng tôi hoan nghênh bạn liên hệ với chúng tôi để giới thiệu thêm sự sáng tạo 📧!

[![Biểu đồ lịch sử sao](https://api.star-history.com/svg?repos=GitHub30/Awesome-Gemini-Robotics&type=Date)](https://www.star-history.com/#GitHub30/Awesome-Gemini-Robotics&Date)

: https://developers.googleblog.com/en/building-the-next-generation-of-physical-agents-with-gemini-robotics-er-15/ "Building the Next Generation of Physical Agents with Gemini Robotics-ER 1.5 - Google Developers Blog"
: https://ai.google.dev/gemini-api/docs/robotics-overview "Gemini Robotics-ER 1.5  |  Gemini API 	|  Google AI for Developers"
: https://blog.google/intl/ja-jp/company-news/technology/gemini-robotics-15-ai/ "Gemini Robotics 1.5 を発表、AI エージェントを物理世界に"
: https://deepmind.google/discover/blog/gemini-robotics-15-brings-ai-agents-into-the-physical-world/ "Gemini Robotics 1.5 brings AI agents into the physical world - Google DeepMind"
