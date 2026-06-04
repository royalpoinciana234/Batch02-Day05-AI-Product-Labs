# Report App Teardown: V-App — V-AI

**Sinh viên:** Nguyễn Quang Hoa — 2A202600986

## 0. Thông tin nhanh

**Sản phẩm:** V-App (Viettel)
**AI feature:** V-AI — trợ lý voice/text, gợi ý theo ngữ cảnh
**Case dùng thử:** User dùng giọng nói để hỏi về cước data còn lại và muốn gia hạn gói
**Input quan sát:** "Gói data của anh còn bao nhiêu, đăng ký lại giúp anh gói cũ."
**Evidence:** Hội thoại voice → text trong app, screenshot màn hình trả lời của V-AI

Teardown này không chấm điểm câu trả lời hay hay dở. Mục tiêu là tìm chỗ workflow gãy: user nói một câu chứa **2 intent** (tra cứu + hành động giao dịch), nhưng V-AI chỉ xử lý được một nửa, không xác nhận giao dịch và không có đường lùi khi nhận diện sai bằng giọng nói.

## 1. Product được chọn

| Hạng mục | Nội dung |
|---|---|
| Product | V-App (Viettel) |
| AI feature | V-AI — trợ lý voice/text, gợi ý theo ngữ cảnh |
| User chính trong case | Thuê bao Viettel muốn tra cứu và thao tác dịch vụ bằng giọng nói |
| Task kỳ vọng | Nói một câu tự nhiên để vừa tra cứu data vừa gia hạn gói cước cũ |

## 2. Promise vs Reality

### Promise

V-AI hứa là trợ lý hiểu ngôn ngữ tự nhiên (cả giọng nói) và **gợi ý theo ngữ cảnh**. Với một câu vừa hỏi vừa ra lệnh, user kỳ vọng:

- V-AI đọc đúng số data còn lại trên tài khoản thật.
- V-AI hiểu "gói cũ" là gói user đang/đã dùng gần nhất.
- V-AI dẫn user qua một bước xác nhận trước khi trừ tiền.
- Nếu nhận diện giọng nói sai, user sửa được mà không phải làm lại từ đầu.

### Reality

V-AI trả lời được phần tra cứu data, nhưng phần "đăng ký lại gói cũ" thì rơi: hoặc bỏ qua, hoặc đẩy user sang một danh sách gói chung chung để tự chọn lại. Câu lệnh giao dịch bị tụt xuống thành điều hướng, không thành hành động có xác nhận.

Điểm gãy chính:

- Chỉ xử lý 1 trong 2 intent trong cùng một câu (tra cứu OK, giao dịch rớt).
- Không phân giải được "gói cũ" thành một gói cụ thể có tên/giá.
- Không có bước **xác nhận giao dịch** (số tiền, thời hạn) trước khi thực hiện.
- Khi voice nhận sai (ví dụ nghe "gói cũ" thành "gói ưu"), không có cách sửa nhanh.
- Không lưu lại lựa chọn để lần sau gợi ý đúng.

### Impact

User tưởng đã ra lệnh xong, nhưng thực tế gói chưa được gia hạn. Rủi ro lớn nhất: user **tưởng đã đăng ký** rồi hết data giữa chừng, hoặc ngược lại bị trừ tiền một gói không định mua vì voice nhận sai mà không có bước xác nhận chặn lại.

## 3. Evidence quan sát được

**Prompt/input đã thử (voice):**
"Gói data của anh còn bao nhiêu, đăng ký lại giúp anh gói cũ."

**Hành vi quan sát được:**
V-AI trả về số dung lượng còn lại, kèm câu dẫn kiểu "Anh xem các gói data tại đây" và mở danh sách gói. Phần "gói cũ" không được nhận diện thành một gói cụ thể; không có câu hỏi xác nhận, không có nút "Gia hạn gói X — 70.000đ/30 ngày — xác nhận".

**Vì sao evidence này quan trọng:**
Câu của user là **multi-intent + giao dịch tiền**. Đây đúng là chỗ trợ lý voice dễ gãy nhất: nhận diện sai cộng với thiếu bước xác nhận có thể dẫn tới mất tiền hoặc mất dịch vụ. Một trợ lý "gợi ý theo ngữ cảnh" mà bỏ rơi vế hành động thì lời hứa context-aware chưa thành.

## 4. Bốn paths

| Path | Hiện trạng quan sát | Kỳ vọng tốt hơn |
|---|---|---|
| Happy path | Trả đúng data còn lại, nhưng dừng ở việc mở danh sách gói. | Tách câu thành 2 intent: trả lời data + nhận diện "gói cũ" thành gói cụ thể, rồi đưa thẻ xác nhận gia hạn. |
| Low-confidence path | Không thấy bước hỏi lại khi "gói cũ" mơ hồ. | Khi không chắc gói nào, V-AI hỏi lại tối đa 3 lựa chọn: gói dùng gần nhất, gói đang chạy, hoặc xem tất cả. |
| Failure path | Voice nhận sai bị xử lý âm thầm, user không biết AI hiểu gì. | Hiển thị lại câu đã nghe ("Em nghe: gia hạn gói cũ — đúng không ạ?") để user xác nhận/sửa trước khi làm. |
| Correction path | Không có sửa nhanh, không lưu lựa chọn. | Cho sửa bằng 1 chạm hoặc nói lại; lưu gói vừa chọn để lần sau "gói cũ" map đúng; có hủy/hoàn tác trước khi trừ tiền. |

## 5. Finding viết thành product decision

Khi user nói "Gói data còn bao nhiêu, đăng ký lại gói cũ", V-AI xử lý câu như **một** intent tra cứu và đánh rơi vế giao dịch, đồng thời không phân giải được "gói cũ" và không có bước xác nhận.

Hậu quả: user tưởng đã gia hạn nhưng chưa, hoặc có nguy cơ bị trừ tiền sai do voice nhận diện lệch mà không có chốt xác nhận.

Lỗi thuộc các layer:

- **Promise layer:** "Gợi ý theo ngữ cảnh" nhưng bỏ rơi ngữ cảnh "gói cũ" và vế hành động.
- **Intent layer:** Không tách được multi-intent (tra cứu + giao dịch) trong cùng một câu.
- **Data-tool layer:** Không map "gói cũ" → một gói cụ thể từ lịch sử thuê bao.
- **Safety layer:** Giao dịch trừ tiền nhưng không có bước xác nhận → rủi ro tài chính.
- **UX recovery layer:** Voice nhận sai không có echo lại + sửa nhanh.

Product decision đề xuất: thêm **Voice Transaction Confirm Flow** cho mọi câu lệnh giọng nói có chứa hành động phát sinh phí.

## 6. Sketch As-is / To-be

| As-is flow hiện tại | To-be flow đề xuất |
|---|---|
| 1. User nói: "Gói data còn bao nhiêu, đăng ký lại gói cũ." | 1. User nói cùng câu trên. |
| 2. V-AI nhận diện chủ yếu vế "data còn bao nhiêu". | 2. V-AI tách 2 intent: (a) tra cứu data, (b) gia hạn "gói cũ". |
| 3. Trả về số data còn lại. | 3. Trả data còn lại + echo: "Em nghe: gia hạn gói cũ, đúng không ạ?" |
| 4. Mở danh sách gói chung để user tự chọn. | 4. Map "gói cũ" → gói dùng gần nhất; nếu mơ hồ thì hỏi lại 2-3 lựa chọn. |
| 5. Không có bước xác nhận trừ tiền. | 5. Hiện thẻ xác nhận: tên gói, giá, thời hạn, nút Xác nhận/Hủy. |
| 6. Không lưu lựa chọn, không hoàn tác. | 6. Sau xác nhận: thông báo kết quả, lưu gói cho lần sau, cho hoàn tác trong cửa sổ ngắn. |

**Điểm gãy lớn nhất trong As-is:** Vế giao dịch trừ tiền bị tụt thành điều hướng, không có xác nhận → rủi ro user mất tiền hoặc mất dịch vụ.

**Điểm sửa chính trong To-be:** Chuyển câu lệnh voice có phí thành flow có echo, phân giải gói, xác nhận và hoàn tác.

## 7. SPEC change đề xuất

### REQ-VAI-VOICE-TRANSACTION-CONFIRM

Khi câu lệnh (text hoặc voice) của user chứa hành động phát sinh phí (gia hạn/đăng ký/mua gói), V-AI không được thực hiện hoặc bỏ qua âm thầm. V-AI phải chạy confirm flow.

Yêu cầu chi tiết:

1. Tách multi-intent trong một câu; xử lý cả vế tra cứu lẫn vế hành động.
2. Với input voice, echo lại nội dung đã nhận diện để user xác nhận/sửa.
3. Phân giải tham chiếu mơ hồ như "gói cũ" thành một gói cụ thể từ lịch sử thuê bao.
4. Nếu không chắc, hỏi lại tối đa 3 lựa chọn ngắn.
5. Trước khi trừ tiền, hiển thị thẻ xác nhận: tên gói, giá, thời hạn.
6. Chỉ thực hiện sau khi user xác nhận tường minh.
7. Cho phép sửa nhanh bằng 1 chạm hoặc nói lại.
8. Hiển thị kết quả sau giao dịch và cho hoàn tác trong cửa sổ ngắn.
9. Lưu lựa chọn để lần sau gợi ý đúng.
10. Nếu không đủ quyền/thông tin, handoff sang màn hình Gói cước hoặc tổng đài.

## 8. Test cases

| Test case | Input / tình huống | Expected behavior |
|---|---|---|
| TC01 - Multi-intent | Voice: "Data còn bao nhiêu, đăng ký lại gói cũ." | V-AI trả data còn lại VÀ khởi tạo flow gia hạn, không bỏ rơi vế giao dịch. |
| TC02 - "Gói cũ" mơ hồ | Lịch sử có nhiều gói gần đây. | V-AI hỏi lại 2-3 lựa chọn (gói dùng gần nhất / đang chạy / xem tất cả). |
| TC03 - Voice nhận sai | V-AI nghe "gói cũ" thành "gói ưu". | V-AI echo lại nội dung đã nghe, user sửa được trước khi trừ tiền. |
| TC04 - Xác nhận giao dịch | User chọn gói 70.000đ/30 ngày. | V-AI hiện thẻ xác nhận giá + thời hạn; chỉ trừ tiền sau khi user bấm Xác nhận; có hoàn tác. |

## 9. Kết luận

Finding quan trọng nhất: vấn đề của V-AI ở case này không phải đọc sai số data, mà là **đánh rơi vế hành động có phí** và **thiếu bước xác nhận cho giao dịch voice**. Với trợ lý giọng nói chạm tới tiền của user, một bước echo + xác nhận + hoàn tác là bắt buộc, không phải tính năng phụ.

SPEC nên đổi theo hướng thêm **Voice Transaction Confirm Flow**: tách multi-intent, phân giải tham chiếu mơ hồ như "gói cũ", echo lại nội dung voice, xác nhận trước khi trừ tiền và cho hoàn tác.
