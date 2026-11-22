# Xếp Ván

Extension nesting tấm ván cho SketchUp, tập trung vào **chống bay ván khi cắt CNC**.

> Bản 0.1 là bản thử nghiệm. Hãy mở DXF trong phần mềm CAM và cắt thử một tấm trước khi sản xuất.

## Cài đặt

1. Tải file `.rbz` mới nhất ở [Releases](https://github.com/mylinkvnn-maker/xep-van/releases/latest).
2. Trong SketchUp: **Extensions → Extension Manager → Install Extension**, chọn file vừa tải.
3. Mở **Extensions → Xếp Ván**.

Những lần sau extension tự báo khi có bản mới (kiểm tra mỗi ngày một lần), hoặc bấm
**Extensions → Xếp Ván → Kiểm tra cập nhật…**

Đã thử trên SketchUp 2024 (Windows). Các phiên bản khác chưa thử.

## Cách dùng

1. Mỗi tấm ván là một group hoặc component kín (solid).
2. Với ván có vân: bấm **Dán tem vân**, bấm vào mặt ngửa của tấm. Tab hoặc → để xoay mũi tên.
3. Chọn tủ, bấm **Quét vùng chọn**.
4. Kiểm tra cột bề mặt của từng vật liệu, khổ ván và khung cảnh báo.
5. Bấm **Xếp tấm**, rồi **Vẽ vào model** hoặc **Xuất file**.

## Kịch bản nesting

| Trường hợp | Cách xếp |
|---|---|
| Vân gỗ, hoa văn, có tem | Khóa theo mũi tên tem, mặt dán tem đặt ngửa |
| Vân gỗ, hoa văn, chưa có tem | Khóa theo lựa chọn mặc định, có cảnh báo |
| Màu đơn sắc | Tự xoay ngang dọc để ít ván nhất |
| Tấm nhỏ (dưới 0,15 m²) hoặc hẹp (dưới 150 mm) | Đưa vào giữa, cắt trước |
| Tấm lớn | Ưu tiên ra 4 phía, cắt sau |
| Ván quá đặc | Chuyển tấm nhỏ sang ván thoáng hơn, không tăng số ván |
| Ván chỉ có tấm nhỏ | Gom cụm vào giữa ván |

Loại bề mặt được đoán theo vật liệu: có ảnh texture, hoặc tên có chữ như vân, gỗ, sồi, oak thì coi là có vân.
Tên chỉ là mã số và không có texture sẽ bị coi là đơn sắc, **hãy kiểm tra lại cột bề mặt**.

Tấm tô đỏ là tấm nhỏ không thể đưa vào trong: nên thêm tab hoặc chừa da khi cắt.

## Lớp trong DXF (đơn vị mm)

| Lớp | Ý nghĩa |
|---|---|
| `KHOAN_D35_SAU12_5` | Khoan phi 35, sâu 12,5 |
| `KHOAN_THUNG_D8` | Khoan thủng phi 8 |
| `HOC_SAU6` | Hốc, rãnh sâu 6 |
| `CAT_THUNG` | Khoét thủng bên trong |
| `CAT_TAM_NHO` | Cắt rời tấm nhỏ, hẹp: **chạy trước** |
| `CAT_BIEN` | Cắt rời tấm lớn: **chạy sau cùng** |
| `KHO_VAN`, `TEN_TAM` | Tham khảo, không cắt |

Phần mềm CAM có thể tự sắp lại đường chạy dao, hãy đặt thứ tự theo lớp như trên.

File `_MAT_SAU.dxf` dành cho mặt sau, ván lật quanh cạnh dài (trục X, Y mới = rộng ván − Y).

## Giới hạn của bản 0.1

- Xếp theo hình chữ nhật bao, chưa lồng các tấm cong hoặc chữ L vào nhau.
- Chưa đọc mặt vát, lỗ khoan ngang ở cạnh, rãnh ở cạnh. Các khối này bị bỏ qua và có cảnh báo.
- Không đọc tem của phần mềm khác.
- Chưa tính nẹp dán cạnh, chưa xuất G-code.
- Kéo tấm trong sơ đồ đã vẽ không làm đổi file xuất.
