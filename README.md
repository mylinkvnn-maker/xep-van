# Xếp Ván

Extension nesting tấm ván cho SketchUp, tập trung vào **chống bay ván khi cắt CNC**.

> Đây là bản thử nghiệm. Hãy mở DXF trong phần mềm CAM và cắt thử một tấm trước khi sản xuất.

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

## Model vẽ bằng ABF

Xếp Ván đọc thẳng tấm ABF, không cần dán tem lại:

- Chiều chữ trên tem ABF là chiều vân, mặt có tem đặt ngửa khi cắt.
- Lỗ khoan, chén bản lề, rãnh, hạ nền nằm trên mặt tấm được đưa vào DXF với **đúng tên lớp ABF**
  (ví dụ `ABF-D15`, `ABF-D35`, `HANEN7`). Độ sâu nằm trong thiết lập ABF, không nằm trong model,
  nên hãy gán dao và độ sâu theo tên lớp trong phần mềm CAM như khi dùng ABF.
- Tên lớp được đổi sang chữ hoa không dấu, dấu cách thành `_` (DXF R12 không nhận dấu).
- Nét kéo quá mép tấm được cắt theo mép tấm; lỗ tròn có tâm ngoài tấm bị bỏ và có cảnh báo.
- Tấm được kéo dài bằng scale: lỗ tròn giữ đường kính gốc, chỉ dời tâm theo tấm.
- Chưa đưa vào DXF, có cảnh báo từng tấm: lỗ khoan cạnh, rãnh ABF tự tính khi xuất (như rãnh hậu).
- Sơ đồ nesting và khổ ván do ABF vẽ trong model được bỏ qua.

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

## Tối ưu số ván

Khung tổng hợp ghi số ván đã xếp và **số ván tối thiểu theo lý thuyết** (tính theo diện tích và các tấm
quá nửa ván). Kết quả không thể ít hơn con số này; thực tế thường hơn 1–2 ván vì khe cắt và cắt suốt.

Mức **Kỹ** tìm lâu hơn (khoảng 20 giây mỗi vật liệu) và có thể bớt ván với đơn hàng lớn. Chống bay vẫn
chạy sau khi số ván đã chốt, nên không bao giờ làm tăng số ván.

Cả hai mức đều thử xếp theo dải (hợp với tấm cùng cỡ), rồi thử dỡ ván thưa nhất vào khe các ván khác.
Không bớt được ván thì tấm được dồn về các ván đầu, phần thừa gom về ván cuối thành đầu thừa lớn.
Mỗi màu và độ dày là một nhóm riêng, xếp trên ván riêng.

**Lồng tấm vào tấm khoét:** tấm chữ L, góc cong, khuyết có khoảng trống trong chữ nhật bao. Trước khi xếp,
tấm nhỏ hơn được lồng vào khoảng trống đó (lớn trước), cách đường bao tấm chủ ít nhất một đường kính dao,
rồi đi theo tấm chủ như một khối. Tấm có vân vẫn đúng chiều vân.

## Dao cắt và cách mép ván

- **Dao cắt:** chọn dao 3, 4, 5, 6 hoặc 8 mm. Đường kính dao là khe giữa hai tấm kề nhau.
- **Cách mép ván:** chọn 0 đến 30 mm, phần mép ván bỏ đi (mép sứt, mép không thẳng).

## Tem nhãn

- Trên sơ đồ, mỗi tấm có một tem: **mã · thứ tự cắt**, tên, kích thước. Bỏ chọn lớp `TEM_NHAN` để ẩn tem.
- Bấm **In tem nhãn** để mở tem khổ 70 × 40 mm trong trình duyệt, bấm **In**. Tem có số ván, thứ tự cắt,
  hình dáng tấm và mũi tên chiều vân, xếp theo ván rồi thứ tự cắt. **Xuất file** cũng lưu `tem_nhan.html`.

## Lớp đường dao trên sơ đồ

Sơ đồ hiện mọi nét gia công với đúng tên lớp sẽ có trong DXF, mỗi lớp một màu. Bấm vào tên lớp ở hàng
"Lớp đường dao" để ẩn hoặc hiện. Nét liền là mặt ngửa, nét đứt là mặt sau, tô trắng là khoan hoặc khoét thủng.
Khi **Vẽ vào model**, mỗi lớp nằm trên một tag cùng tên (mặt sau có tiền tố `MAT_SAU_`).

## Cắt lật tấm

Tấm có hốc, lỗ không thủng hoặc nét ABF ở mặt dưới được dồn vào ít ván nhất, không làm tăng tổng số ván.
Ván phải lật có chữ **LẬT** trên sơ đồ và xuất hai file:

1. `…_1_MAT_SAU.dxf`: chạy trước, khi các tấm còn liền ván. Chỉ có khoan, phay; lớp `VIEN_THAM_KHAO`
   là viền tấm để đối chiếu, **không cắt**.
2. Lật ván theo cách đã chọn (quanh cạnh dài hoặc cạnh ngắn), tựa lại đúng góc gốc máy.
3. `…_2_MAT_TRUOC.dxf`: gia công mặt trước rồi cắt rời.

Ván không có gia công mặt sau chỉ có một file `….dxf`.

## Giới hạn hiện tại

- Tấm lồng chỉ vào chỗ khuyết ở mép tấm chủ (trong chữ nhật bao), chưa vào lỗ khoét thủng bên trong tấm.
  Tấm chủ đã chứa tấm lồng thì không bị lồng tiếp vào tấm khác.
- Chưa đọc mặt vát, lỗ khoan ngang ở cạnh, rãnh ở cạnh. Các khối này bị bỏ qua và có cảnh báo.
- Đọc được tem ABF, chưa đọc tem của phần mềm khác.
- Chưa tính nẹp dán cạnh, chưa xuất G-code.
- Kéo tấm trong sơ đồ đã vẽ không làm đổi file xuất.
