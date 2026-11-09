# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` | Xe ô tô cỡ nhỏ từ 4 đến 9 chỗ, thiết kế chủ yếu để chở người/gia đình. Có cấu trúc thân xe dạng sedan, hatchback, SUV, CUV hoặc MPV. | Ví dụ: Toyota Vios, Hyundai Accent, Mazda CX-5.  Phân biệt: Không bao gồm xe bán tải (thùng hở) và xe tải van (thùng kín đồng nhất). |
| `truck` | Xe cơ giới có cấu trúc khoang chở hàng độc lập (thùng tách biệt phía sau cabin) hoặc xe đầu kéo chuyên dụng. | Ví dụ: Xe tải ben, xe bồn, xe đầu kéo container, xe tải nhỏ chở hàng đô thị. Phân biệt: Luôn bao gồm cả Xe bán tải (Pickup Truck) như Ford Ranger, Toyota Hilux vì có kết cấu thùng hàng hở phía sau. |
| `bus` | Xe ô tô kích thước lớn chuyên dụng để chở nhiều hành khách (thường từ 10 chỗ trở lên). | Ví dụ: Xe buýt nội đô, xe khách giường nằm, xe đưa đón học sinh. Phân biệt: Bao gồm cả dòng xe 16 chỗ (Ford Transit, Hyundai Solati) nếu đang cấu hình chở khách. |
| `van` | Xe có kết cấu hình khối hộp chữ nhật đồng nhất giữa khoang cabin và khoang chứa đồ (không tách rời, có cửa lùa bên hông hoặc cửa hai cánh phía sau). | Ví dụ: Mercedes-Benz Sprinter, Ford Transit (bản chở hàng/hoán cải), Suzuki Blind Van, Kia Morning Van. Phân biệt: Nếu xe 16 chỗ đã tháo hết ghế đằng sau để chở hàng, dán kín kính -> gán nhãn van (thay vì bus).|

## Quy tắc chung

- Box bám sát vật thể nghĩa là: Vẽ Bounding Box ôm sát tối đa các điểm rìa ngoài cùng của vật thể Tight Box. Phải bao gồm cả các bộ phận nhô ra như gương chiếu hậu, lốp dự phòng gắn phía sau, thanh cản va, hoặc hàng hóa chất cao vượt quá thùng xe tải. Không để thừa khoảng trống quá 2-3 pixel.
- Vật thể bị che khuất: Nếu vật thể bị che khuất một phần bởi cây cối, cột đèn, rào chắn hoặc xe khác, vẫn phải vẽ hộp ước lượng toàn bộ vật thể dựa trên hình dáng suy đoán của nó, miễn là phần lộ ra chiếm từ 30% trở lên và mắt người vẫn nhận diện chính xác được loại xe. Nếu bị che khuất > 70% và không thể đoán định, bỏ qua không gán nhãn.
- Vật thể bị cắt bởi rìa ảnh: Vẽ hộp ôm sát phần vật thể xuất hiện trong khung hình. Biên của hộp nhãn sẽ trùng khít với đường biên cắt của rìa ảnh. Chỉ gán nhãn nếu phần vật thể còn lại trong ảnh đủ để nhận diện phân loại lớp thường là còn giữ được phần đầu, phần đuôi hoặc kết cấu bánh xe đặc trưng.
- Vật thể rất nhỏ/mơ hồ: Các vật thể ở quá xa đường chân trời hoặc bị nhòe nặng, có kích thước dưới 15x15 pixel hoặc mắt người không thể tự tin phân biệt giữa 4 lớp xe thì không gán nhãn nhằm tránh tạo ra nhãn nhiễu làm giảm độ chính xác của mô hình AI.


## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_...` | | | |
| `drive_...` | | | |
| `drive_...` | | | |
