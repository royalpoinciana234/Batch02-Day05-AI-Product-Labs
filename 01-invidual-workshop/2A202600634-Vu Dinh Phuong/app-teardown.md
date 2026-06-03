# Workshop — Mổ App AI Thật: Vietnam Airlines NEO

**Sản phẩm:** Vietnam Airlines — NEO  
**AI feature:** Chatbot hỗ trợ đặt vé và giải đáp thông tin  
**Cách truy cập:** Website/Zalo VNA

---

## 2. Dùng thử: promise vs reality

**Product hứa gì?**  
NEO hứa hỗ trợ tìm kiếm chuyến bay, tra cứu giá vé và đặt vé tự động thông qua việc thu thập thông tin hành trình từ chat tự nhiên của khách hàng.

**User nào được hứa sẽ được giúp?**  
Hành khách muốn đặt vé nhanh hoặc kiểm tra giá vé máy bay thông qua giao diện hội thoại mà không cần phải tự thao tác trên form web.

**Kỳ vọng AI làm được task nào?**  
Trích xuất chính xác thông tin hành trình (Slot filling/Entity extraction) như Điểm đi, Điểm đến, Ngày đi, Số lượng khách. Giữ ngữ cảnh để thu thập các thông tin còn thiếu (Slot filling). Khi gặp câu lệnh mơ hồ hoặc mâu thuẫn hành trình (ví dụ hai điểm đến), hệ thống phải nhận diện được để hỏi lại xác nhận.

**Điểm gãy khi dùng thật:**

**Evidence 1 — Phân tích sai thực thể & Tự ý gán slot khi gặp câu lệnh mâu thuẫn (Entity Extraction & Auto-mapping Conflict)** [Image](./image.png)  
*   **User query:** *“tôi muốn đặt vé đi sài gòn, hãy kiểm tra thông tin giá vé đi đi hàn quốc”*
*   **Hành vi của bot:** User dùng tiền tố "đi" cho cả Sài Gòn và Hàn Quốc. NEO không phát hiện mâu thuẫn này để kích hoạt luồng làm rõ (disambiguation). Thay vào đó, nó tự gán bừa Điểm khởi hành là Sài Gòn (SGN) và Điểm đến là Hàn Quốc (ICN), sau đó chỉ yêu cầu điền ngày đi.

**Evidence 2 — Luồng thu thập slot còn thiếu chưa tối ưu về mặt UX (Suboptimal UX for Slot-filling)** [Image](./image2.png)  
*   **User query:** *“tôi muốn đặt vé từ Hà Nội đi Sài Gòn”*
*   **Hành vi của bot:** Bot nhận dạng đúng điểm đi/đến nhưng yêu cầu điền thêm Ngày đi & Số lượng khách dưới dạng văn bản thuần túy. Việc bắt buộc người dùng nhập liệu bằng bàn phím thay vì cung cấp bộ chọn ngày (date picker) hoặc các nút chọn nhanh số lượng khách khiến luồng hội thoại trở nên thủ công và dễ gây sai sót.

**Evidence 3 — Xác nhận thông tin và hiển thị kết quả chuyến bay (Happy Path & Result Delivery)** [Image 1](./datve_1.png) & [Image 2](./datve_2.png)  
*   **User query:** *“Giá vé bay từ Hà Nội đi Sài Gòn hiện tại, cho 1 người lớn ngày 04/06/2026”*
*   **Hành vi của bot:** Khi được cung cấp đầy đủ thông tin, bot tổng hợp chính xác các thực thể, gửi yêu cầu xác nhận (Có/Không) bằng text. Sau khi user xác nhận "có", bot hiển thị danh sách 3 chuyến bay với giá vé và giờ bay cụ thể cùng nút [Đặt vé ngay]. Tuy nhiên, nút [Đặt vé ngay] là CTA tĩnh, chưa dẫn tới link đặt vé đã điền sẵn thông tin.

---

## 3. Vẽ 4 paths

| Path | Quan sát trên NEO | Ý nghĩa product |
|---|---|---|
| **Happy** | User nhập đầy đủ thông tin (như ở `datve_1.png`), bot tổng hợp, xác nhận và hiển thị 3 lựa chọn chuyến bay (như ở `datve_2.png`). | Bot hoạt động tốt khi user nhập dữ liệu sạch, có cấu trúc rõ ràng. |
| **Low-confidence** | Khi câu hỏi chứa hai địa danh cùng tiền tố hướng đi "đi" (như ở `image.png`), bot phát hiện độ tin cậy trích xuất slot thấp và hỏi lại để làm rõ hành trình trước khi gán. | Cần cơ chế xác nhận thông tin khi độ tự tin phân tích thấp (Confidence-based Clarification). |
| **Failure** | Bot tự gán Sài Gòn làm điểm đi và Hàn Quốc làm điểm đến mà không hỏi lại xác nhận, dẫn đến sai lệch hành trình nếu user muốn đi hành trình khác. | Hệ thống thiếu bộ lọc mâu thuẫn (Conflict detection) trong trích xuất thực thể. |
| **Correction** | Hệ thống yêu cầu user tự cung cấp thông tin còn thiếu hoặc không hợp lệ bằng cách gõ lại, nhưng không có các nút bấm sửa đổi nhanh trực quan. | Cần UX Recovery dưới dạng interactive chips để sửa đổi nhanh các slot đã thu thập. |

---

## 4. Viết finding thành quyết định

**Finding 1:**

```
Khi user nhập câu lệnh chứa thông tin mâu thuẫn hoặc mơ hồ về hành trình (hai địa danh đều đi kèm từ khóa định hướng "đi"),
AI tự động gán đại một địa danh làm điểm khởi hành và một địa danh làm điểm đến mà không xác nhận lại,
hậu quả là hệ thống ghi nhận sai lệch thông tin hành trình của khách hàng và chuyển sang bước tiếp theo với dữ liệu sai.
Lỗi thuộc layer Intent + Data-tool (Slot-filling / Entity Extraction).
Nên sửa bằng low-confidence path: Khi phát hiện mâu thuẫn hoặc độ tự tin trích xuất Điểm đi/Điểm đến dưới ngưỡng (e.g. < 85%), hệ thống phải kích hoạt câu hỏi xác nhận hành trình (ví dụ: "Có phải bạn muốn đặt vé từ Sài Gòn đi Hàn Quốc không?").
```

**Finding 2:**

```
Khi hiển thị các thông tin đã thu thập được cho user kiểm tra hoặc yêu cầu bổ sung thông tin thiếu (như ở image2.png và datve_1.png),
AI hiển thị text tĩnh và bắt user gõ văn bản thuần, không cung cấp các nút tương tác nhanh (như date picker hay nút bấm số lượng khách),
hậu quả là trải nghiệm điền thông tin bị kéo dài, dễ gây lỗi chính tả/định dạng khi gõ.
Lỗi thuộc layer UX Recovery / Conversational UX.
Nên sửa bằng cách hiển thị các slot đã thu thập dưới dạng các interactive chips/inputs để user có thể click sửa nhanh, đồng thời hiển thị bộ chọn ngày (date-picker) và nút tăng giảm số lượng hành khách trong khung chat.
```

---

## 5. Sketch as-is / to-be

**Flow 1: Xử lý hành trình mâu thuẫn / mơ hồ**

```
AS-IS                                   TO-BE
─────────────────────────────           ──────────────────────────────────
User chat: "đi Sài Gòn...               User chat: "đi Sài Gòn...
đi Hàn Quốc" (Mâu thuẫn)                đi Hàn Quốc" (Mâu thuẫn)
         ↓                                       ↓
NEO tự động gán slot:                   Bộ phân tích phát hiện độ tự tin thấp
- Điểm đi: Sài Gòn                      hoặc mâu thuẫn slot (Cả 2 đều là "đi")
- Điểm đến: Hàn Quốc                             ↓
         ↓                              Kích hoạt Low-confidence path:
NEO yêu cầu cung cấp ngày đi            Bot đưa ra câu hỏi xác nhận hành trình
(Bỏ qua lỗi sai hành trình)             "Bạn muốn bay SGN -> ICN đúng không?"
         ↓                                       ↓
[ĐIỂM GÃY]                              User xác nhận bằng nút bấm
User nhận kết quả tìm kiếm sai,         hoặc nhập lại hành trình đúng
hoặc phải chat lại từ đầu
```

**Flow 2: Đính chính thông tin đã trích xuất (UX Recovery)**

```
AS-IS                                   TO-BE
─────────────────────────────           ──────────────────────────────────
Bot hiển thị thông tin trích xuất       Bot hiển thị thông tin dưới dạng
(text tĩnh)                             các Interactive Chips (nút bấm)
         ↓                                       ↓
User phát hiện bot nhận diện sai        User phát hiện điểm đi bị sai
(Ví dụ: Điểm đi phải là Hà Nội)                  ↓
         ↓                              User click vào chip "Điểm đi: Sài Gòn"
[ĐIỂM GÃY]                                       ↓
Không có nút bấm sửa nhanh,             Hệ thống hiện danh sách chọn lại
user phải gõ giải thích lại             hoặc cho phép nhập lại điểm đi mới
```

**Flow 3: Điền thông tin còn thiếu (Slot filling UX)**

```
AS-IS                                   TO-BE
─────────────────────────────           ──────────────────────────────────
Bot yêu cầu cung cấp Ngày đi và         Bot hiển thị Date-picker trực tiếp
Số lượng khách bằng văn bản             và bộ nút chọn số lượng hành khách
         ↓                                       ↓
User phải gõ tay bằng bàn phím          User click chọn ngày trên lịch chat
(ví dụ: "04/06/2026", "1 người lớn")    và click tăng/giảm số lượng khách
         ↓                                       ↓
Dễ sai định dạng ngày tháng             Thông tin được truyền về chuẩn xác
```

---

## Tự kiểm trước khi nộp

- [x] Có ít nhất 1 screenshot hoặc observation cụ thể — Phân tích chi tiết lỗi trích xuất thực thể hành trình mâu thuẫn [Image](./image.png), luồng điền slot thiếu [Image](./image2.png) và happy path hoàn chỉnh [Image](./datve_1.png) [Image](./datve_2.png).
- [x] Có đủ 4 paths — Happy, Low-confidence, Failure, Correction đều có phân tích cụ thể.
- [x] Finding được viết thành product decision — 2 findings theo format trigger/failure/impact/layer/fix.
- [x] Sketch có as-is và to-be — 3 flows thể hiện rõ điểm gãy và luồng cải tiến.
- [x] Có một câu nói rõ finding này sẽ đổi gì trong SPEC: NEO cần bổ sung cơ chế **Conflict detection (nhận diện mâu thuẫn thực thể)**, **Low-confidence fallback (hỏi xác nhận)** và **Rich UI components (Date-picker, Interactive Chips, passenger selector)** vào SPEC để nâng cao hiệu quả thu thập slot hành trình và cải thiện UX đặt vé.
