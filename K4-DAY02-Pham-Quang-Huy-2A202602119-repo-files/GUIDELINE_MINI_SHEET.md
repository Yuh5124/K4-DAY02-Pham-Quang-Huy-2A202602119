# Phiếu quy tắc gán nhãn — Ngày 2

**Họ và tên:** PHẠM QUANG HUY<br>
**MSSV:** 2A202602119<br>
**Hình thức:** CÁ NHÂN — cá nhân hoặc theo cặp<br>
**Mã cặp:** SOLO — ghi `SOLO` nếu làm cá nhân

## 1\. Phạm vi

* Chỉ gán phương tiện thuộc bốn lớp bên dưới.
* Mỗi phương tiện là một hộp; không gộp nhiều xe.
* Không gán người, xe máy, xe đạp, biển báo hoặc phần phản chiếu.
* Vật thể quá nhỏ hoặc mờ đến mức không thể phân lớp có căn cứ: không đoán; ghi lý do vào nhật ký quyết định.

## 2\. Bốn lớp cố định

|Mã|Lớp|Gán khi nhìn thấy|Không gán vào lớp này|
|-:|-|-|-|
|0|`car` (ô tô con)|sedan, hatchback, SUV, taxi, xe bán tải dùng như xe con|xe có thùng/ben rõ; thân xe buýt; xe van thân hộp|
|1|`truck` (xe tải)|thùng, ben, sàn hàng hoặc thiết bị công vụ rõ ràng|ô tô con; thân xe buýt; xe van kín một khối|
|2|`bus` (xe buýt)|thân xe khách dài, nhiều cửa sổ hoặc hàng ghế|xe van nhỏ; xe tải; ô tô con|
|3|`van` (xe van)|thân hộp nhỏ, kín, dùng chở người hoặc hàng|thân xe buýt; khoang hàng tách biệt như xe tải|

Thứ tự lớp là cố định: `0 car, 1 truck, 2 bus, 3 van`.

## 3\. Hộp giới hạn

* Vẽ sát phần vật thể nhìn thấy.
* Không ước lượng phần bị xe khác che.
* Vật thể chạm mép ảnh vẫn được gán nếu đủ bằng chứng phân lớp.
* Không để hộp chứa nhiều nền hoặc nhiều phương tiện.

## 4\. Ba thuộc tính

|Thuộc tính|Giá trị|Ý nghĩa|
|-|-|-|
|`visibility` (mức nhìn thấy)|`clear` (rõ), `occluded` (bị che), `unclear` (không rõ)|mức bằng chứng nhìn thấy|
|`boundary` (quan hệ mép ảnh)|`inside` (trong ảnh), `truncated` (bị cắt)|vật thể có bị mép ảnh cắt hay không|
|`review\_state` (trạng thái xem lại)|`confident` (tự tin), `needs\_review` (cần xem lại)|đánh dấu quyết định cần quay lại|

YOLO không lưu ba thuộc tính này. Vì vậy phải xuất thêm `CVAT for images 1.1` từ cùng công việc.

## 5\. Ba tình huống mơ hồ

Hoàn thành trước khi xem bài của người khác hoặc bộ nhãn tham chiếu.

### Tình huống A — xe buýt hay xe van?

* Ảnh và mã vật thể: drive\_022, hộp tại pixel xyxy ≈ (99, 341) → (390, 577)
* Dấu hiệu nhìn thấy: thân xe dài hai khoang nối bằng khớp xếp (xe buýt khớp nối), nhiều cửa sổ dọc thân, có bảng số hiệu tuyến điện tử phía trước, sơn theo tông xe buýt công cộng (cam–xám–xanh dương). Thuộc tính bạn đã gán trong CVAT: visibility=clear, boundary=inside, review\_state=confident.
* Quy tắc áp dụng: mục 2, dòng bus — "thân xe khách dài, nhiều cửa sổ hoặc hàng ghế"; loại trừ van vì thân không phải khối hộp kín nhỏ.
* Quyết định: bộ tham chiếu gán hộp này là van, còn bạn (đã đánh dấu confident) gán là bus. Theo mô tả hình học ở trên, các dấu hiệu khớp với định nghĩa bus trong phiếu hơn. Bạn hãy tự mở lại ảnh drive\_022 để xác nhận, rồi ghi quyết định cuối và trích đúng câu quy tắc bạn dùng.
Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? 
Không tự đổi nhãn của mình chỉ vì bộ tham chiếu gán khác — hình học trùng khít nhưng lớp lệch nhau là tín hiệu cần kiểm tra chéo, không phải bằng chứng bộ tham chiếu đúng. Tôi sẽ: (1) phóng to lại vùng hộp trên ảnh gốc để tìm thêm chi tiết (khớp nối thân xe, số cửa, hàng ghế); (2) đối chiếu lại với đúng câu chữ trong bảng lớp ở mục 2; (3) nếu sau khi kiểm vẫn không đủ căn cứ để chọn một bên, đánh dấu review\_state=needs\_review, ghi rõ hai khả năng vào nhật ký quyết định, và hỏi Lab Coach thay vì tự đoán hoặc tự sửa theo bộ tham chiếu.

### Tình huống B — xe tải hay xe van/ô tô con?

* Ảnh và mã vật thể: drive\_008, hộp tại pixel xyxy ≈ (142, 120) → (201, 185)
* Dấu hiệu nhìn thấy: xe nhìn từ phía sau, thân hộp nhỏ kín, nóc bằng, có cửa hậu hai cánh, không thấy thùng/ben hở. Thuộc tính bạn đã gán: visibility=clear, boundary=inside, review\_state=confident.
* Quy tắc áp dụng: mục 2, dòng van — "thân hộp nhỏ, kín, dùng chở người hoặc hàng"; loại trừ truck vì không thấy thùng/ben/sàn hàng hở.
* Quyết định: bộ tham chiếu gán hộp này là truck, còn bạn gán là van. Theo mô tả hình học, đặc điểm khớp van hơn. Bạn hãy tự xem lại ảnh gốc (có thể xe có phần thùng phía sau bị góc chụp che khuất) rồi ghi quyết định cuối.
* Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? 
Vì góc chụp từ camera trên cao thường che khuất đúng phần quyết định (thùng xe tải hay cửa hậu van), tôi sẽ: (1) tìm góc/khung hình khác của cùng xe nếu có trong ảnh (ví dụ xe xuất hiện ở vị trí khác trong chuỗi khung hình); (2) nếu chỉ có một góc nhìn duy nhất và không thấy rõ phần thân sau, ưu tiên dấu hiệu chắc chắn nhất đang có (ở đây là "không thấy thùng/ben hở" → nghiêng về van) thay vì đoán; (3) đánh dấu review\_state=needs\_review kèm lý do cụ thể, rồi hỏi Lab Coach hoặc bạn cùng cặp thay vì tự ý sửa theo bộ tham chiếu.

### Tình huống C — bị che, bị mép ảnh cắt hay không đủ bằng chứng?

* Ảnh và mã vật thể: drive_038, hai hộp sát mép trái tại pixel xyxy ≈ (0, 182.6) → (26, 229.6) và (0, 183.6) → (54.4, 268.8)
* Dấu hiệu nhìn thấy khi phóng 100%: ả hai hộp đều nằm sát mép trái (x bắt đầu đúng bằng 0), phần thân xe (một xe van/minivan màu sẫm) bị mép ảnh cắt ngang; hai hộp có kích thước khác nhau nhưng đè lên gần như cùng một vị trí — nhiều khả năng là hai hộp trùng lặp cho cùng một xe thay vì hai xe riêng biệt.
* Giá trị `visibility`: hộp 1 = occluded, hộp 2 = clear
* Giá trị `boundary`: hộp 1 = truncated, hộp 2 = inside
* Trạng thái `review\_state`: hộp 1 = needs\_review, hộp 2 = confident
* Lý do: xe nằm đúng mép ảnh nên phần thân bên trái chắc chắn bị cắt bởi khung hình (không phải bị xe khác che) — boundary=truncated hợp lý hơn inside. Việc có hai hộp lệch thuộc tính cho cùng một vị trí là dấu hiệu trùng lặp cần dọn trước khi nộp. Bạn hãy tự kiểm tra trong CVAT: nếu đúng là một xe, xoá một hộp và giữ lại hộp có boundary=truncated; nếu là hai xe khác nhau, ghi rõ bằng chứng phân biệt.

## 6\. Xác nhận tự kiểm tra

* \[X] Đã rà đủ bốn ảnh.
* \[X] Đã kiểm vật thể thiếu và trùng.
* \[X] Đã kiểm lớp và hình học từng hộp.
* \[X] Mỗi hộp có đủ ba thuộc tính.
* \[X] Đã xử lý mọi hộp `needs\_review`.
* \[X] Đã hoàn thành ba tình huống trước khi xem nguồn đối chiếu.
* \[X] Nếu làm theo cặp, hai người đã xuất bài độc lập trước khi trao đổi.
* \[X] Nếu làm cá nhân, bài riêng đã được kiểm trước khi nhận bộ tham chiếu.
* \[X] Số vật thể thực tế: 85 hộp — 40–60 là mục tiêu khối lượng, không phải điểm cắt.

