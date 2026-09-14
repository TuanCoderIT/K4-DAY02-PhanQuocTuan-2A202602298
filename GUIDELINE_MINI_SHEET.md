# Phiếu quy tắc gán nhãn — Ngày 2

**Họ và tên:** PHAN QUỐC TUẤN<br>
**MSSV:** 2A202602298<br>
**Hình thức:** CÁ NHÂN<br>
**Mã cặp:** SOLO

## 1. Phạm vi

- Chỉ gán phương tiện thuộc bốn lớp bên dưới.
- Mỗi phương tiện là một hộp; không gộp nhiều xe.
- Không gán người, xe máy, xe đạp, biển báo hoặc phần phản chiếu.
- Vật thể quá nhỏ hoặc mờ đến mức không thể phân lớp có căn cứ: không đoán; ghi lý do vào nhật ký quyết định.

## 2. Bốn lớp cố định

| Mã | Lớp | Gán khi nhìn thấy | Không gán vào lớp này |
| ---: | --- | --- | --- |
| 0 | `car` (ô tô con) | sedan, hatchback, SUV, taxi, xe bán tải dùng như xe con | xe có thùng/ben rõ; thân xe buýt; xe van thân hộp |
| 1 | `truck` (xe tải) | thùng, ben, sàn hàng hoặc thiết bị công vụ rõ ràng | ô tô con; thân xe buýt; xe van kín một khối |
| 2 | `bus` (xe buýt) | thân xe khách dài, nhiều cửa sổ hoặc hàng ghế | xe van nhỏ; xe tải; ô tô con |
| 3 | `van` (xe van) | thân hộp nhỏ, kín, dùng chở người hoặc hàng | thân xe buýt; khoang hàng tách biệt như xe tải |

Thứ tự lớp là cố định: `0 car, 1 truck, 2 bus, 3 van`.

## 3. Hộp giới hạn

- Vẽ sát phần vật thể nhìn thấy.
- Không ước lượng phần bị xe khác che.
- Vật thể chạm mép ảnh vẫn được gán nếu đủ bằng chứng phân lớp.
- Không để hộp chứa nhiều nền hoặc nhiều phương tiện.

## 4. Ba thuộc tính

| Thuộc tính | Giá trị | Ý nghĩa |
| --- | --- | --- |
| `visibility` (mức nhìn thấy) | `clear` (rõ), `occluded` (bị che), `unclear` (không rõ) | mức bằng chứng nhìn thấy |
| `boundary` (quan hệ mép ảnh) | `inside` (trong ảnh), `truncated` (bị cắt) | vật thể có bị mép ảnh cắt hay không |
| `review_state` (trạng thái xem lại) | `confident` (tự tin), `needs_review` (cần xem lại) | đánh dấu quyết định cần quay lại |

YOLO không lưu ba thuộc tính này. Vì vậy phải xuất thêm `CVAT for images 1.1` từ cùng công việc.

## 5. Ba tình huống mơ hồ

Hoàn thành trước khi xem bài của người khác hoặc bộ nhãn tham chiếu.

### Tình huống A — xe buýt hay xe van?

- Ảnh và mã vật thể: `drive_038` (các xe dạng thân hộp ở khoảng giữa dòng giao thông)
- Dấu hiệu nhìn thấy: Phương tiện có hình dáng thân hộp vuông dài, tuy nhiên tỷ lệ chiều dài thân xe ngắn hơn xe buýt thông thường và không thấy rõ các cửa sổ lớn xếp hàng dài đặc đặc trưng của xe buýt.
- Quy tắc áp dụng: Theo quy tắc Mục 2: Xe buýt (`bus`) cần có thân xe khách dài và nhiều cửa sổ/hàng ghế; xe van (`van`) có thân hộp nhỏ, kín chở người hoặc hàng.
- Quyết định: Phân loại vào lớp `van`.
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Phóng to 100%, quan sát tỷ lệ chiều dài so với bánh xe và kính chắn gió; nếu vẫn không thể khẳng định sẽ đặt `review_state` = `needs_review` và xin hỗ trợ từ Lab Coach.

### Tình huống B — xe tải hay xe van/ô tô con?

- Ảnh và mã vật thể: `drive_038` (phương tiện chở hàng ở làn đường giao thông xa)
- Dấu hiệu nhìn thấy: Nhìn thấy cabin phía trước kết hợp với khối thùng phía sau, nhưng ranh giới giữa khoang lái và thùng hàng không hoàn toàn rõ ràng do độ phân giải ảnh ở xa.
- Quy tắc áp dụng: Lớp `truck` đòi hỏi phải thấy thùng, ben hoặc sàn hàng tách biệt rõ ràng; nếu là thân hộp kín liền khối thì thuộc lớp `van`.
- Quyết định: Phân loại vào lớp `truck` nếu quan sát thấy khoảng vạch chia tách giữa cabin và thùng hàng.
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Đánh dấu thuộc tính `visibility` = `unclear`, đặt `review_state` = `needs_review` để ghi nhận điểm mơ hồ trước khi chốt nhãn.

### Tình huống C — bị che, bị mép ảnh cắt hay không đủ bằng chứng?

- Ảnh và mã vật thể: `drive_033` (phương tiện di chuyển sát viền ảnh và bị xe khác đi song song)
- Dấu hiệu nhìn thấy khi phóng 100%: Một phần đuôi xe bị viền khung hình cắt qua, đồng thời phần bên hông bị xe chạy bên cạnh che khuất một phần.
- Giá trị `visibility`: `occluded`
- Giá trị `boundary`: `truncated`
- Trạng thái `review_state`: `confident`
- Lý do: Mặc dù vừa bị cắt bởi mép ảnh vừa bị che bởi vật thể khác, các đặc điểm nhận dạng chính của lớp `car` vẫn quan sát được đầy đủ để phân loại có căn cứ.

## 6. Xác nhận tự kiểm tra

- [x] Đã rà đủ bốn ảnh.
- [x] Đã kiểm vật thể thiếu và trùng.
- [x] Đã kiểm lớp và hình học từng hộp.
- [x] Mỗi hộp có đủ ba thuộc tính.
- [x] Đã xử lý mọi hộp `needs_review`.
- [x] Đã hoàn thành ba tình huống trước khi xem nguồn đối chiếu.
- [x] Nếu làm theo cặp, hai người đã xuất bài độc lập trước khi trao đổi.
- [x] Nếu làm cá nhân, bài riêng đã được kiểm trước khi nhận bộ tham chiếu.
- [x] Số vật thể thực tế: 90 — 40–60 là mục tiêu khối lượng, không phải điểm cắt.
