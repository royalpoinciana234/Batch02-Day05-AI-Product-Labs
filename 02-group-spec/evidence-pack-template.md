# Template — Evidence Pack (Completed)

Nộp kèm thin SPEC cuối Day 05.

## 1. Nhóm và track

**Tên nhóm:** Nhóm 1 - AI Healthcare Kickstarters  
**Track:** E · Healthcare  
**Product/app đã chọn:** Vinmec, Long Châu, Pharmacity  
**Build slice đang nghĩ:** Prescription Explainer (Paste văn bản đơn thuốc -> giải thích các thông tin thuốc dễ hiểu -> tạo bảng lịch uống thuốc cụ thể trong ngày).

## 2. Self-use evidence

Nhóm tự dùng app/workflow và ghi lại điểm gãy.

| Observation | Screenshot/link | Path liên quan | Điều học được |
|---|---|---|---|
| Chụp đơn thuốc giấy có nhiều ký hiệu viết tắt như "1v x 2 (s-t)", "uống sau ăn" gửi lên app Long Châu. App nhận diện sai dạng bào chế (nhầm viên uống Cravit thành thuốc nhỏ mắt Cravit) hoặc sai hàm lượng do ký tự viết tay bác sĩ mờ. | [longchau_app_ocr_failure.png](file:///home/henry/Downloads/AI%20in%20action/day5/Batch02-Day05-AI-Product-Labs/02-group-spec/longchau_app_ocr_failure.png) | Failure Path | OCR đơn thuốc viết tay có tỷ lệ sai số y khoa cực kỳ cao. Không được tự ý đưa thẳng thuốc vào giỏ hàng mà không có sự kiểm duyệt của Dược sĩ và xác nhận từ User. |
| Đơn thuốc Vinmec in máy rõ ràng nhưng nhiều tên hoạt chất khó hiểu (ví dụ: *Augmentin 1g*, *Nexium mups 40mg*). Bệnh nhân không nhớ rõ thuốc nào điều trị triệu chứng nào (nhiễm trùng hay dạ dày) và uống lúc nào cho tối ưu. | [vinmec_print_prescription.png](file:///home/henry/Downloads/AI%20in%20action/day5/Batch02-Day05-AI-Product-Labs/02-group-spec/vinmec_print_prescription.png) | Happy/Low-confidence Path | Dù đơn thuốc in máy rõ ràng, người bệnh vẫn gặp rào cản ngôn ngữ y khoa chuyên môn và thiếu một lịch trình uống thuốc cụ thể cho từng buổi trong ngày. |

## 3. User / review / social evidence

Nguồn có thể là review App Store/Play, group, comment, phỏng vấn nhanh, hoặc nguồn public khác.

| Quote / review / observation | Nguồn | User là ai? | Pain/failure mode |
|---|---|---|---|
| "Bác sĩ kê đơn ghi uống sáng tối, nhưng không nói rõ uống trước ăn hay sau ăn bao lâu. Có thuốc uống chung được không? Tra Google thì hoang mang quá." | Group "Hỏi bác sĩ nhi" (Facebook) | Người mẹ chăm con ốm | Đơn thuốc thiếu thông tin chi tiết về thời điểm uống tối ưu và tương tác thuốc cơ bản. |
| "App Vinmec hiển thị lịch sử khám tốt, nhưng phần đơn thuốc chỉ là ảnh PDF. Mỗi lần xem phải tải về zoom to rất bất tiện, không tạo được nhắc nhở uống thuốc." | Review trên Google Play Store | Bệnh nhân điều trị ngoại trú | UI đơn thuốc dạng tĩnh (PDF) không hỗ trợ hành động thực tế tiếp theo của bệnh nhân (uống thuốc đúng giờ). |
| "Nhiều khi uống thuốc xong lại quên không biết mình đã uống hay chưa, đặc biệt là người già uống 4-5 loại một ngày rất dễ bị trùng liều." | Phỏng vấn nhanh người thân trong gia đình | Người lớn tuổi bị bệnh mãn tính | Quên trạng thái uống thuốc, nguy cơ uống quá liều hoặc thiếu liều. |

## 4. Competitor / analog evidence

| App / mô hình tham khảo | Họ xử lý task này thế nào? | Pattern học được | Có áp dụng trong 1 ngày không? |
|---|---|---|---|
| **Medisafe** (App quản lý thuốc nổi tiếng thế giới) | Nhập tên thuốc thủ công, tự động gợi ý lịch và liều lượng. Hiển thị hình ảnh dạng viên thuốc trực quan để dễ phân biệt. | Chuyển đơn thuốc thành lịch trình trực quan bằng các biểu tượng và màu sắc sinh động để tăng tuân thủ điều trị. | Chỉ áp dụng một phần: Trích xuất lịch từ đơn bằng AI rồi render bảng tĩnh trong 1 ngày, phần báo thức động để sau. |
| **Vinmec App** | Chỉ lưu trữ tệp PDF đơn thuốc tĩnh. Không có giải thích hay tương tác. | Cung cấp nguồn dữ liệu gốc đáng tin cậy nhưng trải nghiệm sử dụng thuốc tại nhà bị đứt gãy. | Có. Ta có thể lấy văn bản thô từ đơn thuốc làm đầu vào. |

## 5. Evidence -> Insight

```text
Evidence nổi bật nhất:
Bệnh nhân nhận đơn thuốc nhưng gặp khó khăn lớn khi tự dịch các thuật ngữ chuyên môn hoặc viết tắt của bác sĩ sang lịch trình hành động hàng ngày. Việc chụp và OCR ảnh đơn thuốc viết tay thường xuyên gặp lỗi nghiêm trọng về liều lượng và tên biệt dược.

Insight:
User không chỉ gặp khó khăn ở việc đọc chữ trên đơn (surface problem).
Thật ra họ cần sự an tâm khi sử dụng thuốc và một lịch trình hành động cụ thể, rõ ràng, dễ thực hiện hàng ngày (decision support & trust) để tự tin tuân thủ điều trị mà không sợ sai sót.

Opportunity:
AI có thể giúp bằng cách phân tách nội dung đơn thuốc dạng văn bản (paste text), giải nghĩa từ chuyên môn một cách dễ hiểu và tự động sắp xếp thành bảng lịch uống thuốc trực quan theo buổi trong ngày (Sáng, Trưa, Chiều, Tối).
```

## 6. Evidence đổi SPEC như thế nào?

- [ ] Đổi user chính.
- [ ] Đổi pain statement.
- [x] Đổi build slice.
- [x] Đổi Auto/Aug decision.
- [x] Đổi 4 paths.
- [x] Đổi failure mode.
- [ ] Đổi owner/test plan.

Ghi rõ 1-2 thay đổi quan trọng:

```text
Trước evidence, nhóm định:
- Cho phép người dùng chụp ảnh đơn thuốc, dùng AI OCR tự động phân tích và thêm các loại thuốc trực tiếp vào giỏ hàng mua sắm tự động (Automation).

Sau evidence, nhóm đổi thành:
- Bỏ tính năng OCR ảnh đơn thuốc trong phiên bản đầu tiên để tránh lỗi nhận diện y khoa nguy hiểm. Chỉ nhận đầu vào là văn bản đơn thuốc (copy-paste text).
- Thay đổi quyết định từ Automation sang Augmentation: AI chỉ phân tích và đề xuất bảng thuốc kèm lịch uống mẫu, hiển thị cảnh báo y tế rõ ràng và cho phép người dùng tự xác nhận, chỉnh sửa thủ công (Correction Path) trước khi lưu lịch.

Lý do:
- Rủi ro y khoa của việc nhận diện sai đơn thuốc viết tay là quá lớn. Việc hạ scope xuống nhận text và để người dùng làm chủ quyết định cuối cùng giúp sản phẩm an toàn hơn và khả thi để hoàn thiện prototype xuất sắc chỉ trong vòng 1 ngày.
```
