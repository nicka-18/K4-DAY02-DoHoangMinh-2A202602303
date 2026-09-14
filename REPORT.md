# Báo cáo — Ngày 2: phát hiện vật thể

**Họ và tên:** Đỗ Hoàng Minh<br>
**MSSV:** 2A202602303<br>
**Hình thức:** Cá nhân<br>
**Mã cặp:** `SOLO`

## 1. Bài độc lập và nguồn dữ liệu

- Mã SHA-256 của ZIP ảnh được cấp: f7d99888f21440fb0374d84962b93213bd8c14e665d093cc8d37f4c61b71ed33
- Bốn mã ảnh:
    drive_008.jpg; fde088a7a955343bb3366008d31e3c2a6ecf6ddbfd08e58ecd91f5e6d67a05d1
    drive__022.jpg; cb8297af4cc5bb660f9d56f0e31ed77f8fa1f95b31f47bb8b8a1cd08b2f94886
    drive_033.jpg; 8dc05a7a8f06ed137d08b643ef676629a2c4cf3b25051852de22e7400311e465
    drive_038.jpg; 35294a107b157646e0619ad985226ef424968d7798f90f7ded5b32f32cebbb86
- Số vật thể thực tế: 116
- Mã SHA-256 của gói YOLO của bạn: 8debc06b802d75554f0cbb7aa963d8407ae5273086f50dd3258fa134aaeccab7
- Mã SHA-256 của gói CVAT gốc của bạn: f2fb3b4ee911b4cfca753e70428ce9ff75fd493a021754a874650f0187ba1d95
- Nguồn đối chiếu: bạn cùng cặp hoặc bộ tham chiếu do người hướng dẫn thực hành cấp:
- Mã SHA-256 của gói đối chiếu: c8bbc767d8bb9a29f4ca5abf0c3516e5c2af94c58143a980b0148cfe0b500d2b
- Nếu làm cá nhân, ghi mã lần phát và thời điểm nhận bộ tham chiếu:
    -Mã lần phát: c8bbc767d8bb9a29f4ca5abf0c3516e5c2af94c58143a980b0148cfe0b500d2b
    -Thời điểm nhận file: 21:31 14/09/2026

Giải thích vì sao bài của bạn vẫn độc lập trước khi đối chiếu:

    Không bị can thiệp chéo: Việc đối chiếu chỉ thực hiện sau khi dữ liệu cá nhân đã xuất và lưu trữ nguyên vẹn, đảm bảo toàn bộ quyết định gán nhãn (bounding box và 3 thuộc tính visibility, boundary, review_state) đều phản ánh góc nhìn đánh giá độc lập ban đầu.

## 2. Quyết định phân lớp

| Ảnh/vật thể | Lớp | Dấu hiệu nhìn thấy | Quy tắc áp dụng |
| --- | --- | --- | --- |
| drive_088.jpg; Van 28 | van | Xe dạng khoang kín chở khách/hàng, kích thước trung bình, ngắn hơn xe buýt tiêu chuẩn và không có cabin rời như xe tải.| Phân định theo khung gầm & công năng: Dòng xe chở khách/hàng thương mại cỡ vừa (9–16 chỗ) được gán là van thay vì bus hoặc car. |

Nêu một ví dụ cho thấy lớp và thuộc tính là hai loại thông tin khác nhau:
    -Lớp (Class): Trả lời cho câu hỏi "Vật thể này là GÌ?" — dùng để xác định đối tượng thực thể (Ví dụ: car).
    -Thuộc tính (Attribute): Trả lời cho câu hỏi "Vật thể đó đang ở TRẠNG THÁI NÀO?" — bổ sung chi tiết về điều kiện quan sát của đối tượng (Ví dụ: visibility = occluded, boundary = truncated, review_state = confident).



## 3. Tự kiểm tra và sửa nhãn

| Trước khi sửa | Loại lỗi | Cách phát hiện | Sau khi sửa và quy tắc |
| --- | --- | --- | --- |
| Chọn nhãn car cho xe bán tải (Pickup) ở góc ảnh; visibility để clear dù xe bị cây che khuất. | lớp & thuộc tính | Mở bảng danh sách Object trong CVAT, zoom 100% kiểm tra phần thùng xe sau và vùng điểm ảnh bị bóng cây đè lên. | Đổi nhãn thành truck, sửa visibility thành occluded. Quy tắc: Xe có thùng chở hàng tách biệt gán nhãn truck; đối tượng bị vật khác che khuất một phần phải gắn occluded. |

- Số hộp `needs_review` trước và sau khi kiểm: trước: 5; sau: 0
- Một quyết định chưa đủ bằng chứng và cách bạn xin hỗ trợ:
    -Tình huống: Đối tượng ở mép ảnh drive_038.jpg bị cắt mất 80% thân xe, chỉ lộ ra một phần bánh xe và viền viền vè chắn bùn. Không đủ căn cứ thị giác để xác định chính xác là car hay van.
    -Cách xin hỗ trợ: Giữ nguyên trạng thái review_state = needs_review, chụp lại màn hình phóng to góc quan sát kèm mã Object ID, gửi vào kênh trao đổi bài lab/tài liệu thảo luận của lớp để xin ý kiến thống nhất từ giảng viên (hoặc người kiểm duyệt dữ liệu - Data Reviewer) trước khi khóa nhãn xuất file final.

## 4. Một dòng nhãn YOLO

- Dòng `class x_center y_center width height`: 0 0.265156 0.506328 0.105625 0.071094
- Tên lớp và tọa độ điểm ảnh `xyxy`:
    -Tên lớp: car
    -Tọa độ pixel xyxy: [135.9, 301.3, 203.5, 346.8]
- Vì sao dòng đúng định dạng vẫn có thể sai lớp, phạm vi hoặc hình học?

    Dòng dữ liệu đúng định dạng chỉ đảm bảo tuân thủ cú pháp số học (gồm 5 tham số, các giá trị chuẩn hóa nằm trong khoảng $[0, 1]$), nhưng vẫn có thể sai về ngữ nghĩa hình ảnh vì:Sai lớp: Mã lớp là số nguyên hợp lệ (0), nhưng đối tượng thực tế trong ảnh có thể là xe máy, người đi bộ hoặc xe tải chứ không phải ô tô (car).Sai phạm vi: Khung bao khoanh thiếu đối tượng (chỉ khoanh một phần bánh xe/đầu xe) hoặc khoanh thừa quá nhiều phông nền không liên quan.Sai hình học: Tọa độ và kích thước hợp lệ về mặt số liệu, nhưng khung vẽ thực tế bị lệch tâm, méo, hoặc không ôm sát viền thực thể của xe.

## 5. Huấn luyện và dự đoán thử

- Ba mã ảnh huấn luyện: 
    drive_088.jpg

    drive_022.jpg

    drive_038.jpg
- Mã ảnh thẩm định:
    drive_033.jpg;  8dc05a7a8f06ed137d08b643ef676629a2c4cf3b25051852de22e7400311e465
- Mô tả một dự đoán trong `detect_result.jpg`: 
    Mô hình dự đoán nhãn car với độ tin cậy 0.45 (45%) cho một chiếc xe bán tải (Pickup) nằm ở góc phải ảnh, trong khi gán nhãn thủ công ban đầu của bài là truck
- Dự đoán đó gợi ý cần kiểm lại quy tắc hoặc dữ liệu nào?
    Gợi ý cần rà soát lại quy tắc định nghĩa lớp (Class Boundary) giữa car và truck đối với các dòng xe bán tải (Pickup truck), hoặc tăng số lượng mẫu huấn luyện cho dòng xe bán tải để mô hình phân biệt rõ nét hơn giữa khoang cabin và thùng chở hàng.
- Minh chứng nào có thể bác bỏ nhận định của bạn?
    Nếu hình ảnh nguyên bản ở độ phân giải cao hơn hiển thị rõ xe có kết cấu thân liền khối (Unibody Crossover/SUV) thay vì khung gầm rời có thùng hàng riêng (Body-on-frame), hoặc tập dữ liệu chuẩn hóa của bài quy định tất cả xe dưới 9 chỗ (bao gồm bán tải) đều tính là car.
- Vì sao kết quả trên bốn ảnh không phải phép đánh giá mô hình dùng thực tế?

    Kích thước mẫu quá nhỏ: Số lượng 4 ảnh (với 40–60 bounding box) không đủ tính đại diện thống kê cho các điều kiện giao thực tế (thời tiết, ánh sáng, mật độ).Quá khớp (Overfitting): Huấn luyện trên tập dữ liệu nhỏ dẫn đến việc mô hình chỉ học thuộc lòng 4 bức ảnh thay vì học khả năng tổng quát hóa (Generalization).Thiếu tập kiểm thử độc lập (Test Set): Việc chia tập thẩm định (val) chỉ với 1 ảnh không phản ánh đúng các chỉ số $mAP@0.5$ hay $mAP@0.5:0.95$ thực tế của mô hình khi triển khai.

## 6. Đối chiếu nhãn

- Số hộp ghép được: 48 hộp
- IoU trung bình và trung vị: 
    -IoU trung bình (Mean IoU): 0.88
    -IoU trung vị (Median IoU): 0.91
- Mức đồng thuận lớp: 94%
- Số hộp phía bạn không ghép được: 3 hộp
- Số hộp phía đối chiếu không ghép được: 2 hộp 
- Một điểm khác biệt cụ thể: 
    -Tại ảnh drive_022.jpg, một đối tượng xe chạy ở làn đối diện phía xa: Bài của em gán nhãn car với visibility = clear, trong khi bộ tham chiếu gán nhãn van với visibility = occluded do có rào chắn cố định che mất phần bánh xe.
- Quy tắc hoặc hành động sửa phát sinh: 
    -Thống nhất quy tắc bổ sung: Tất cả các phương tiện bị rào chắn dải phân cách che khuất phần gầm/bánh xe từ 20% trở lên phải cập nhật thuộc tính visibility = occluded, đồng thời kiểm tra kỹ chiều cao khung gầm để phân định car và van.
- Vì sao mức đồng thuận cao không chứng minh mọi nhãn đều đúng?

    -Sai sót hệ thống (Systematic Bias): Cả bạn và bộ tham chiếu có thể cùng mắc chung một sai lầm do hiểu sai quy tắc chung (ví dụ: cùng gán sai toàn bộ xe bán tải thành car thay vì truck).

    -Chỉ phản ánh độ tương đồng, không phản ánh thực tế (Ground Truth): Mức đồng thuận cao (High Agreement) chỉ chứng minh hai bên gán nhãn giống nhau, chứ không đảm bảo các nhãn đó phản ánh chính xác 100% thực tế khách quan của đối tượng trên ảnh.

## 7. Kiểm tra kho GitHub cá nhân

- [x] Có phiếu quy tắc với ba tình huống mơ hồ.
- [x] Có kết quả kiểm hai gói xuất.
- [x] Có thông tin lần huấn luyện và ảnh dự đoán.
- [x] Có tóm tắt, bảng và ảnh phủ của bước đối chiếu.
- [x] Không có gói xuất thô, bộ nhãn tham chiếu hoặc trọng số mô hình.
- [x] Không có dữ liệu VinFast/khách hàng/ảnh cá nhân/mật khẩu/mã truy cập.

Minh chứng mạnh nhất trong bài và câu hỏi còn lại cho Lab Coach:

