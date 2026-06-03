# Thin SPEC — Prescription Explainer (Completed)

Thin SPEC không phải PRD đầy đủ. Đây là bản cam kết đủ rõ để sáng Day 06 nhóm build ngay.

## 1. Track, product/app và user

- **Track:** E · Healthcare
- **Product/app thật:** Vinmec, Long Châu
- **User cụ thể:** Bệnh nhân ngoại trú sau khi khám bệnh trở về nhà, nhận được đơn thuốc (dạng văn bản/in giấy) và tự điều trị/uống thuốc tại nhà.
- **Nhóm có phải user thật không? Nếu không, khác ở đâu?**  
  Thành viên nhóm có thể tự đóng vai trò user thật khi đóng vai người đi khám bệnh ngoại trú hoặc người nhà chăm sóc bệnh nhân. Sự khác biệt nhỏ là thành viên nhóm có kiến thức công nghệ cao hơn mặt bằng chung, do đó cần thiết kế giao diện cực kỳ tối giản, cỡ chữ to rõ ràng để hướng tới cả người lớn tuổi sử dụng.

## 2. Evidence summary

| Evidence | Nguồn | User/pain nói lên điều gì? | SPEC phải đổi gì? |
|---|---|---|---|
| Chụp ảnh đơn thuốc viết tay gửi lên app Long Châu bị nhận diện sai lệch nghiêm trọng về biệt dược và liều lượng. | Thử nghiệm thực tế (Self-use) | OCR chữ viết tay của bác sĩ có rủi ro y khoa cực kỳ cao, dễ gây nguy hiểm cho tính mạng người bệnh. | Loại bỏ tính năng OCR ảnh đơn thuốc ở phiên bản đầu tiên. Chỉ nhận input bằng **Text đơn thuốc (copy-paste)**. |
| Người dùng hoang mang về thời điểm uống thuốc tối ưu khi đơn thuốc chỉ ghi chung chung "uống sau ăn". | Group thảo luận y tế (Facebook) | Bệnh nhân thiếu hướng dẫn chi tiết, trực quan về lịch uống thuốc cụ thể trong ngày. | Thiết kế tính năng tự động tạo bảng lịch uống thuốc cụ thể theo buổi (Sáng, Trưa, Chiều, Tối). |
| Đơn thuốc chỉ là file PDF tĩnh trên app Vinmec, không hỗ trợ hành động thực tế tiếp theo của bệnh nhân. | Đánh giá trên Google Play | Người bệnh muốn tương tác, ghi nhận trạng thái đã uống thuốc để tránh quên hoặc uống trùng liều. | Trích xuất dữ liệu thành bảng tương tác, cho phép đánh dấu trạng thái uống thuốc đơn giản trong ngày. |

## 3. Pain statement

```text
User [bệnh nhân điều trị ngoại trú tại nhà] đang gặp khó ở [bước đọc hiểu đơn thuốc và tự sắp xếp lịch uống thuốc],
vì [đơn thuốc chứa nhiều từ chuyên môn y khoa phức tạp, ký hiệu viết tắt và thiếu một lịch trình trực quan theo thời gian thực tế],
dẫn tới [người bệnh hoang mang, dễ quên thuốc, uống sai liều lượng hoặc tự ý dừng thuốc do lo sợ tác dụng phụ].
Bằng chứng chính là [nhiều review phàn nàn trên các app y tế hiện nay về việc đơn thuốc chỉ là ảnh chụp PDF tĩnh khó xem và các thảo luận của bệnh nhân trên mạng xã hội hỏi về cách phối hợp uống nhiều loại thuốc].
```

## 4. Build slice

```text
Cho [bệnh nhân ngoại trú điều trị tại nhà] đang [bối rối không biết uống thuốc thế nào cho đúng và an toàn],
prototype sẽ dùng AI để [augment việc trích xuất và giải thích đơn thuốc văn bản thô],
tạo ra [bảng danh sách thuốc có cấu trúc rõ ràng cùng lịch uống cụ thể hôm nay theo các buổi Sáng, Trưa, Chiều, Tối],
và xử lý [failure mode: AI trích xuất thiếu hoặc sai thông tin liều dùng] bằng [mitigation: hiển thị cảnh báo y tế bắt buộc, highlight màu vàng các điểm nghi vấn và cho phép user chỉnh sửa trực quan trước khi lưu].
```

## 5. Auto/Aug decision

Chọn một:

- [x] **Augmentation:** AI gợi ý/draft/phân loại, user quyết cuối.
- [ ] **Conditional automation:** AI tự làm trong case hẹp; case mơ hồ/rủi ro chuyển người.
- [ ] **Automation:** AI tự quyết và tự hành động.

- **Lý do chọn:** Lĩnh vực sức khỏe (Healthcare) liên quan trực tiếp tới tính mạng con người. AI không được phép thay thế bác sĩ hay tự quyết định liều lượng. AI chỉ đóng vai trò trợ lý trích xuất và giải nghĩa thông tin y khoa phức tạp thành ngôn ngữ bình dân để người dùng dễ tiếp thu, đồng thời người dùng phải là người kiểm duyệt và phê duyệt cuối cùng (Human-in-the-loop).
- **Human role:** **reviewer / decider** (Người kiểm duyệt và quyết định lưu lịch uống thuốc).

## 6. Four paths

| Path | Prototype phải thể hiện gì? |
|---|---|
| **Happy** | Người dùng paste đơn thuốc rõ ràng (in máy). AI phân tích chính xác 100% các thuốc, công dụng, liều dùng và hiển thị lịch uống dạng timeline đẹp mắt theo các buổi trong ngày (Sáng, Trưa, Chiều, Tối). |
| **Low-confidence** | Đơn thuốc bị thiếu thông tin hoặc chứa chữ viết tắt lạ (ví dụ: "uống khi cần", "n.m.đ"). AI trích xuất các phần rõ ràng, riêng phần thiếu sẽ hiển thị ô nhập màu vàng nổi bật kèm gợi ý: *"AI chưa rõ liều lượng loại này, vui lòng điền thêm hoặc kiểm tra lại với dược sĩ của bạn"*. |
| **Failure** | AI nhận diện sai tên hoạt chất hoặc nhầm lẫn liều lượng (do lỗi định dạng văn bản gốc). Hệ thống luôn hiển thị dòng chữ cảnh báo cố định ở đầu màn hình kết quả: *"Đây là thông tin hỗ trợ do AI tổng hợp, không thay thế chỉ định của bác sĩ. Vui lòng đối chiếu kỹ với đơn gốc trước khi uống"*. |
| **Correction** | Cho phép người dùng nhấn đúp vào bất kỳ ô nào trong bảng thuốc (Tên thuốc, Liều lượng, Công dụng, Giờ uống) để chỉnh sửa thủ công và lưu lại. Bảng lịch uống thuốc sẽ tự động cập nhật ngay lập tức tương ứng với thay đổi đó. |

## 7. Failure mode nguy hiểm nhất

```text
Nếu user [nhập văn bản đơn thuốc bị lỗi font hoặc viết tắt mơ hồ (ví dụ: nhầm 1 viên thành 10 viên hoặc nhầm thuốc nhỏ mắt thành thuốc uống)],
AI có thể [trích xuất sai thông tin liều lượng hoặc phân loại sai thuốc nhưng vẫn hiển thị ở trạng thái tự tin],
hậu quả là [người dùng uống quá liều hoặc uống sai thuốc gây nguy kịch đến sức khỏe].
Prototype sẽ xử lý bằng [luôn hiển thị bảng đối chiếu đơn gốc song song với kết quả của AI, yêu cầu người dùng tích chọn xác nhận từng loại thuốc trước khi sinh lịch uống, và cài đặt giới hạn liều tối đa mặc định cho các thuốc phổ biến (ví dụ: Paracetamol không quá 4g/ngày) để cảnh báo đỏ nếu AI đề xuất vượt ngưỡng].
Owner kiểm thử path này là [Henry].
```

## 8. Owner plan cho sáng Day 06

| Thành viên | Việc phụ trách | Bằng chứng cần có trong repo |
|---|---|---|
| **Henry** | Research / evidence & Thin SPEC | File `evidence-pack.md` và `thin-spec.md` hoàn thành, không có phần bỏ trống. |
| **Partner A** | Prototype UI (Streamlit/HTML) | Mã nguồn Frontend + Backend tích hợp Gemini API chạy ổn định cục bộ. |
| **Partner B** | Test / failure path & Demo | Kịch bản demo chi tiết (Happy/Failure path) và file ghi hình chạy thử nghiệm thành công. |
