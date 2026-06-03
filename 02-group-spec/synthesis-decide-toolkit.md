# Toolkit — Từ Evidence Đến Build Slice (Completed)

Dùng sau khi nhóm đã có evidence. Mục tiêu là chốt một build slice đủ nhỏ cho Day 06.

## 1. Gom evidence thành cụm

Gom theo **workflow/pain**, không gom theo tên feature.

- **Cụm 1: Bất đồng ngôn ngữ y tế (Jargon Overload)**
  - Người dùng nhận đơn thuốc từ bệnh viện lớn (Vinmec) có ghi biệt dược lạ hoặc viết tắt chuyên ngành nhưng không hiểu công dụng chính là gì (ví dụ: uống *Nexium* để làm gì, tại sao lại kết hợp nhiều kháng sinh cùng lúc).
- **Cụm 2: Khó khăn khi thiết lập lịch trình sử dụng (Schedule Fragmentation)**
  - Người dùng lúng túng khi có quá nhiều loại thuốc (4-5 loại) cần uống vào các thời điểm khác nhau (trước ăn, sau ăn, sáng, tối). Việc tự chia lịch dễ dẫn đến quên thuốc hoặc uống sai giờ.
- **Cụm 3: Rủi ro nhận diện sai từ ảnh chụp đơn thuốc (OCR Errors)**
  - Chữ viết tay của bác sĩ rất xấu dẫn đến AI nhận dạng nhầm lẫn tai hại (ví dụ nhầm thuốc uống thành thuốc nhỏ mắt Cravit). Điều này cho thấy tính năng quét ảnh là quá mạo hiểm ở vòng đầu tiên.

## 2. Viết insight

Form:

```text
User [segment] không chỉ cần [surface need].
Họ thật ra cần [deeper need],
vì [evidence pattern].
```

**Bản hoàn thiện:**
Bệnh nhân ngoại trú sau khi khám bệnh không chỉ cần một danh sách các loại thuốc đã mua. Họ thật ra cần một hướng dẫn sử dụng đơn giản, trực quan và một lịch trình uống thuốc an toàn hàng ngày để giảm bớt lo âu và tránh nhầm lẫn, vì nhiều phản hồi thực tế cho thấy họ thường quên giờ giấc hoặc lúng túng khi tự sắp xếp lịch uống cho 4-5 loại thuốc cùng lúc.

## 3. Viết opportunity

Form:

```text
Cơ hội là dùng AI để [augment/automate hành động hẹp],
giúp user [kết quả],
trong khi vẫn kiểm soát [failure/risk].
```

**Bản hoàn thiện:**
Cơ hội là dùng AI để tự động phân tích và trích xuất thông tin từ văn bản đơn thuốc (copy-paste text), giúp user hiểu rõ công dụng từng loại thuốc và tự động sinh lịch uống thuốc trực quan theo các buổi trong ngày (Sáng, Trưa, Chiều, Tối), trong khi vẫn kiểm soát rủi ro bằng cách hiển thị các cảnh báo y tế nổi bật, đánh dấu các điểm thông tin mập mờ để người dùng tự tay xác nhận lại.

## 4. Chọn build slice

Build slice tốt phải qua 5 câu hỏi:

| Câu hỏi | Đạt khi | Kết quả đánh giá của Nhóm |
|---|---|---|
| **User cụ thể chưa?** | Nói được ai dùng, trong bối cảnh nào. | **ĐẠT**. Bệnh nhân ngoại trú vừa nhận đơn thuốc giấy/văn bản sau khi khám bệnh xong tại nhà. |
| **Task đủ hẹp chưa?** | Demo được trong 3-5 phút. | **ĐẠT**. Copy-paste văn bản đơn thuốc -> AI hiển thị bảng giải thích thuốc + bảng lịch uống thuốc hôm nay. |
| **AI decision rõ chưa?** | AI gợi ý/tự làm một việc cụ thể. | **ĐẠT**. AI trích xuất thông tin có cấu trúc (tên thuốc, liều dùng, tần suất, lưu ý) và đề xuất lịch uống tương ứng. |
| **Failure path rõ chưa?** | Có một case AI không chắc hoặc sai để test. | **ĐẠT**. Khi đơn thuốc bị thiếu thông tin liều hoặc chứa ký tự lạ, AI sẽ cảnh báo và hiển thị dấu hiệu nghi vấn thay vì đoán bừa. |
| **Có evidence không?** | Có bằng chứng từ self-use/review/user/competitor. | **ĐẠT**. Có bằng chứng từ lỗi nhận dạng của app Long Châu và phản hồi của người dùng trên diễn đàn về việc khó sắp xếp lịch uống thuốc. |

## 5. Quyết định: giữ, giảm scope, hay đổi hướng?

| Tình huống thực tế | Quyết định của Nhóm | Lý do cụ thể |
|---|---|---|
| **Ý tưởng ban đầu quá rộng** (AI chăm sóc sức khỏe toàn diện) | Cắt giảm scope xuống một flow duy nhất | Tập trung duy nhất vào giải thích đơn thuốc và lập lịch uống thuốc hôm nay từ dữ liệu text đầu vào. |
| **Rủi ro y tế cao** | Chọn Augmentation thay vì Automation | AI chỉ đề xuất và hỗ trợ giải thích, người dùng luôn giữ quyền phê duyệt cuối cùng. Hiển thị cảnh báo y khoa rõ ràng. |
| **Không demo kịp OCR ảnh trong 1 ngày** | Đưa OCR vào Backlog | Chụp ảnh và nhận diện OCR đơn thuốc tay có tỷ lệ sai số cao và tốn thời gian tối ưu hóa, không khả thi cho prototype 1 ngày. |

## 6. Câu chốt cuối

Điền câu này trước khi rời lớp:

```text
Dựa trên [các lỗi nhận dạng y tế nguy hiểm khi dùng thử app đối thủ và nỗi lo âu quên lịch uống thuốc của người bệnh ngoại trú],
nhóm sẽ build [Prescription Explainer prototype],
cho [bệnh nhân điều trị ngoại trú tại nhà],
để giải quyết [pain: khó đọc hiểu đơn thuốc chuyên môn và không biết sắp xếp lịch uống thuốc tối ưu],
bằng cách AI [augment việc trích xuất thông tin thuốc từ text đơn thô thành bảng cấu trúc và tự sinh lịch uống hàng ngày trực quan],
và sẽ test failure path [AI trích xuất thiếu thông tin liều hoặc nhầm lẫn biệt dược bằng cách đánh dấu đỏ cảnh báo và cho phép người dùng sửa đổi trực tiếp].
```

## 7. Backlog

Những thứ **không build trong Day 06**:

- **Backlog 1:** Nhận diện và OCR ảnh chụp đơn thuốc viết tay.
- **Backlog 2:** Push notification nhắc nhở uống thuốc thời gian thực trên thiết bị di động.
- **Backlog 3:** Tích hợp với API nhà thuốc Long Châu / Pharmacity để đặt mua thuốc trực tuyến.
- **Backlog 4:** Theo dõi chỉ số sức khỏe dài hạn (huyết áp, đường huyết) và lưu lịch sử bệnh án.
