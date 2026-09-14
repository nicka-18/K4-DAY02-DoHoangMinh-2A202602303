# Phiếu quy tắc gán nhãn — Ngày 2

**Họ và tên:** Đỗ Hoàng Minh<br>
**MSSV:** 2A202602303<br>
**Hình thức:** Cá nhân<br>
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

- Ảnh và mã vật thể: drive_008.jpg; Van 28
- Dấu hiệu nhìn thấy: Kích thước xe trung bình; cửa sổ dạng kính dài dải dọc thân xe nhưng chiều dài xe ngắn hơn xe buýt tiêu chuẩn; không thấy rõ biển hiệu tuyến bus.
- Quy tắc áp dụng: Quy tắc phân định kích thước & công năng (Dựa trên khung gầm và tỷ lệ chiều dài/chiều cao). Nếu là xe chở khách từ 9–16 chỗ dạng Transit/HiAce --> gán van. Nếu là xe từ 29 chỗ trở lên hoặc xe buýt công cộng --> gán
- Quyết định: van
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Gán nhãn theo dự đoán hợp lý nhất dựa trên tỉ lệ khung hình (bbox) và chọn review_state = needs_review để nhờ người rà soát (reviewer) kiểm tra lại.
### Tình huống B — xe tải hay xe van/ô tô con?

- Ảnh và mã vật thể: drive_008.jpg; Truck 22
- Dấu hiệu nhìn thấy: Phần đầu xe giống SUV/ô tô con nhưng phía sau có thùng chở hàng phẳng/được phủ bạt.
- Quy tắc áp dụng: Quy tắc phân loại thùng hàng. Xe có thùng chở hàng tách biệt hoặc khoang chở hàng lớn thiết kế chuyên dụng chở hàng --> gán truck. Xe gia đình chở khách hoặc SUV 5-7 chỗ --> gán 
- Quyết định: truck
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Giữ nguyên nhãn gán hiện tại, đánh dấu review_state = needs_review và ghi chú nghi vấn vào tài liệu báo cáo Edge Case của buổi gán nhãn.

### Tình huống C — bị che, bị mép ảnh cắt hay không đủ bằng chứng?

- Ảnh và mã vật thể: drive_008.jpg; Car 4
- Dấu hiệu nhìn thấy khi phóng 100%: Đối tượng nằm sát góc ảnh, chỉ nhìn thấy khoảng 20–30% phần đuôi xe/đầu xe.
- Giá trị `visibility`: unclear
- Giá trị `boundary`: truncated
- Trạng thái `review_state`: needs_review
- Lý do: Đối tượng đồng thời chịu hai tác động (vừa chạm mép ảnh vừa bị che khuất). Cần đánh dấu needs_review để cấp quản lý dữ liệu kiểm tra xem đối tượng này có đủ điều kiện giữ lại trong tập huấn luyện YOLO hay cần loại bỏ (drop) để tránh nhiễu mô hình.

## 6. Xác nhận tự kiểm tra

- [x] Đã rà đủ bốn ảnh.
- [x] Đã kiểm vật thể thiếu và trùng.
- [x] Đã kiểm lớp và hình học từng hộp.
- [x] Mỗi hộp có đủ ba thuộc tính.
- [x] Đã xử lý mọi hộp `needs_review`.
- [x] Đã hoàn thành ba tình huống trước khi xem nguồn đối chiếu.
- [ ] Nếu làm theo cặp, hai người đã xuất bài độc lập trước khi trao đổi.
- [x] Nếu làm cá nhân, bài riêng đã được kiểm trước khi nhận bộ tham chiếu.
- [x] Số vật thể thực tế: 116 
