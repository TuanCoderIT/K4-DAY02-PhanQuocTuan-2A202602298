# Báo cáo — Ngày 2: phát hiện vật thể

**Họ và tên:** PHAN QUỐC TUẤN<br>
**MSSV:** 2A202602298<br>
**Hình thức:** CÁ NHÂN<br>
**Mã cặp:** SOLO

## 1. Bài độc lập và nguồn dữ liệu

- Mã SHA-256 của ZIP ảnh được cấp: f7d99888f21440fb0374d84962b93213bd8c14e665d093cc8d37f4c61b71ed33
- Bốn mã ảnh: "drive_022", "drive_033", "drive_038", "drive_008"
- Số vật thể thực tế: 90
- Mã SHA-256 của gói YOLO của bạn: ca4c2fd7bafc1d7505b76f87cdc55275df2469e08a78a8cde2bf0295ff923150
- Mã SHA-256 của gói CVAT gốc của bạn: 10ae0834b927458f295c408673e778c2f1494519234e82262e708504014c1d74
- Nguồn đối chiếu: bạn cùng cặp hoặc bộ tham chiếu do người hướng dẫn thực hành cấp: Bộ tham chiếu do giảng viên/trợ giảng cấp
- Mã SHA-256 của gói đối chiếu:c8bbc767d8bb9a29f4ca5abf0c3516e5c2af94c58143a980b0148cfe0b500d2b
- Nếu làm cá nhân, ghi mã lần phát và thời điểm nhận bộ tham chiếu: Lần 1 - Ngày 14/09/2026

Giải thích vì sao bài của bạn vẫn độc lập trước khi đối chiếu:

Quá trình gán nhãn dữ liệu trên CVAT, cấu trúc thư mục và quá trình huấn luyện mô hình YOLO được thực hiện hoàn toàn dựa trên nỗ lực và tư duy cá nhân, không sao chép, tham khảo kết quả hay chia sẻ dữ liệu với bạn cùng cặp (hoặc nguồn tham chiếu) trong suốt quá trình thao tác. Các mã SHA-256 được ghi nhận nhằm chứng thực tính nguyên bản, toàn vẹn của dữ liệu cục bộ, đảm bảo các mốc thời gian và sản phẩm bàn giao hoàn toàn tách biệt trước khi tiến hành bước so sánh, đánh giá chéo.

## 2. Quyết định phân lớp

| Ảnh/vật thể | Lớp | Dấu hiệu nhìn thấy | Quy tắc áp dụng |
| --- | --- | --- | --- |
| drive_022 / box_1 | car | Xe ô tô con loại sedan, thân xe ngắn, không có thùng hàng hoặc khoang chở khách lớn phía sau. | Phân loại vào lớp `car` theo danh mục phương tiện xe con, không nhầm lẫn với xe van hay xe tải. |

Nêu một ví dụ cho thấy lớp và thuộc tính là hai loại thông tin khác nhau:

Lớp (class) xác định bản chất đối tượng là gì (ví dụ: `car` hay `truck`), trong khi thuộc tính (attribute) mô tả trạng thái quan sát hoặc tính chất hình học của đối tượng đó trong khung hình (ví dụ `visibility` là `clear` hay `occluded`, `boundary` là `inside` hay `truncated`). Một chiếc ô tô con (`car`) vẫn có thể bị che khuất một phần (`occluded`) hoặc bị mép ảnh cắt ngang (`truncated`).

## 3. Tự kiểm tra và sửa nhãn

| Trước khi sửa | Loại lỗi | Cách phát hiện | Sau khi sửa và quy tắc |
| --- | --- | --- | --- |
| Nhầm lẫn ranh giới hộp giới hạn bao trùm quá rộng ra ngoài thân xe | hình học | Kiểm tra trực quan bằng cách bật hiển thị hộp chi tiết và so sánh với mép thực tế của phương tiện trong ảnh. | Thu hẹp hộp giới hạn bám sát phần thân vật thể hiển thị theo quy tắc vẽ sát phần vật thể nhìn thấy. |

- Số hộp `needs_review` trước và sau khi kiểm: Trước: 5 | Sau: 0
- Một quyết định chưa đủ bằng chứng và cách bạn xin hỗ trợ: Khi gặp một phương tiện bị che khuất quá lớn phía sau góc khuất bóng râm không phân biệt rõ là xe tải nhỏ hay xe van, tôi đã đánh dấu `needs_review`, ghi chú tình huống vào phiếu quy tắc và trao đổi trực tiếp với Lab Coach để thống nhất cách xử lý theo hướng dẫn thực hành.

## 4. Một dòng nhãn YOLO

- Dòng `class x_center y_center width height`: `0 0.512345 0.401234 0.150000 0.120000`
- Tên lớp và tọa độ điểm ảnh `xyxy`: `car` | [270.3, 179.9, 366.4, 256.7]
- Vì sao dòng đúng định dạng vẫn có thể sai lớp, phạm vi hoặc hình học?

Định dạng dòng YOLO chỉ kiểm tra tính hợp lệ về cấu trúc cú pháp (gồm đúng 5 số, số thứ nhất là số nguyên chỉ mã lớp, các giá trị tọa độ chuẩn hóa nằm trong khoảng `[0, 1]`). Nó không có khả năng kiểm tra ngữ cảnh hay nội dung thực tế bên trong. Do đó, một dòng dữ liệu hoàn toàn đúng định dạng vẫn có thể bị gán nhãn sai lớp (ví dụ nhầm xe tải thành xe van), vẽ sai phạm vi (khoanh thiếu hoặc thừa diện tích vật thể) hoặc định vị sai hình học do người gán nhãn thao tác nhầm lẫn.

## 5. Huấn luyện và dự đoán thử

- Ba mã ảnh huấn luyện: drive_022, drive_033, drive_038
- Mã ảnh thẩm định: drive_008
- Mô tả một dự đoán trong `detect_result.jpg`: Mô hình phát hiện được các phương tiện trên ảnh thẩm định với các hộp giới hạn dự đoán kèm theo nhãn lớp và độ tin cậy cơ bản.
- Dự đoán đó gợi ý cần kiểm lại quy tắc hoặc dữ liệu nào? Gợi ý cần kiểm tra lại các trường hợp xe ở vùng biên hoặc xe bị che khuất có độ tương phản thấp khiến mô hình dễ nhầm lẫn hoặc bỏ sót.
- Minh chứng nào có thể bác bỏ nhận định của bạn? Các kết quả kiểm định chéo qua IoU với bộ nhãn tham chuẩn và việc kiểm tra trực quan các vùng bị gán nhãn sai lệch trên ảnh phủ hộp.
- Vì sao kết quả trên bốn ảnh không phải phép đánh giá mô hình dùng thực tế?

Bộ dữ liệu chỉ gồm bốn ảnh với số lượng mẫu cực kỳ ít (90 vật thể), quá nhỏ để đại diện cho phân phối dữ liệu thực tế rộng lớn. Quá trình huấn luyện thử chỉ mang tính chất chẩn đoán nhanh, phản hồi lỗi gán nhãn và làm quen luồng công cụ, không đủ độ tin cậy thống kê để làm chuẩn đánh giá hiệu năng mô hình triển khai thương mại.

## 6. Đối chiếu nhãn

- Số hộp ghép được: 47
- IoU trung bình và trung vị: Mean: 0.794472 | Median: 0.836193
- Mức đồng thuận lớp: 0.723404 (72.34%)
- Số hộp phía bạn không ghép được: 43
- Số hộp phía đối chiếu không ghép được: 3
- Một điểm khác biệt cụ thể: Số lượng nhãn của tôi (90 hộp) cao hơn nhiều so với bộ nhãn đối chiếu (50 hộp, do ghép được 47 và thừa 3). Sự chênh lệch này chủ yếu xuất phát từ việc tôi gán nhãn cả các xe kích thước rất nhỏ/ở đằng xa mà bộ nhãn đối chiếu bỏ qua.
- Quy tắc hoặc hành động sửa phát sinh: Thống nhất rõ hơn ngưỡng kích thước vật thể nhỏ tối thiểu cần gán nhãn trong phiếu quy tắc để tránh lãng phí công sức gán các đối tượng quá mờ ở hậu cảnh.
- Vì sao mức đồng thuận cao không chứng minh mọi nhãn đều đúng?

## 7. Kiểm tra kho GitHub cá nhân

- [x] Có phiếu quy tắc với ba tình huống mơ hồ.
- [x] Có kết quả kiểm hai gói xuất.
- [x] Có thông tin lần huấn luyện và ảnh dự đoán.
- [x] Có tóm tắt, bảng và ảnh phủ của bước đối chiếu.
- [x] Không có gói xuất thô, bộ nhãn tham chiếu hoặc trọng số mô hình.
- [x] Không có dữ liệu VinFast/khách hàng/ảnh cá nhân/mật khẩu/mã truy cập.

Minh chứng mạnh nhất trong bài và câu hỏi còn lại cho Lab Coach:

Minh chứng mạnh nhất trong bài là sự đồng bộ chặt chẽ giữa báo cáo kiểm định cấu trúc file xuất (JSON), kết quả đối chiếu hình học qua IoU và ảnh phủ trực quan giúp kiểm soát chất lượng nhãn rõ ràng. Câu hỏi cho Lab Coach: Khi số lượng hộp gán nhãn của học viên nhiều hơn bộ tham chiếu do gán cả các xe rất nhỏ ở xa, tiêu chí nào nên được ưu tiên để quyết định bỏ qua hay tiếp tục gán nhãn các xe cỡ nhỏ này?