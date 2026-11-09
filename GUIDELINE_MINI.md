# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` | sedan, SUV, taxi, pickup, xe bán tải | xe nhỏ |
| `truck` | xe tải, xe đầu kéo | xe có thùng |
| `bus` | xe buýt, minibus | xe to dài |
| `van` | xe van, minivan | xe chở hàng nhỏ |

## Quy tắc chung

- Box bám sát vật thể nghĩa là: box không chứa khoảng cách tới vật thể.
- Vật thể bị che khuất: là vật nằm sau vật thể khác, bị che bởi cây cối.
- Vật thể bị cắt bởi rìa ảnh: là vật thể nằm ngoài khung hình.
- Vật thể rất nhỏ/mơ hồ: là vật thể ở xa, mờ

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_028.jpg` | nhiều xe cùng màu đen với tán cây | Không gán nhãn | không xác định được xe hay là bóng tán cây |
| `drive_026.jpg` | nhiều xe bus nối nhau, có một loại xe bus có thiết kế sọc đen ở giữa, khiến AI nhận diện đó là khúc nối giữa 2 xe | gán loại xe đó là 1 box | |
| `drive_037.jpg` | vật thể bị che bơi cây | không gán nhãn | không xác định được loại xe |
