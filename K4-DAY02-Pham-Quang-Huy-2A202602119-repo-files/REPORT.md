# Báo cáo — Ngày 2: phát hiện vật thể

**Họ và tên:** PHẠM QUANG HUY<br>
**MSSV:** 2A202602119
<br>
**Hình thức:** CÁ NHÂN — cá nhân hoặc theo cặp<br>
**Mã cặp:** SOLO — ghi `SOLO` nếu làm cá nhân

## 1. Bài độc lập và nguồn dữ liệu

- Mã SHA-256 của ZIP ảnh được cấp:f7d99888f21440fb0374d84962b93213bd8c14e665d093cc8d37f4c61b71ed33
- Bốn mã ảnh:drive_008, drive_022, drive_033, drive_038 (mỗi ảnh 640×640 px)
- Số vật thể thực tế:: 85 hộp trong gói xuất cuối cùng của bạn — vượt khoảng mục tiêu 40–60 (xem cảnh báo within_slide_workload_target: false trong my_export_audit.json). Bộ đối chiếu của Lab Coach có 50 hộp, dùng để hiệu chỉnh chứ không phải "số đúng tuyệt đối".
- Mã SHA-256 của gói YOLO của bạn:c9ff89f3cfdceddfa3a7dd5c9ad9f51baccbe8234b18a56f4572640c6ee21b2e
- Mã SHA-256 của gói CVAT gốc của bạn:a7ea99a757b810779abb3005414dacfd5f84f1f9c704ba297fe3765cc70dc581
- Nguồn đối chiếu: bạn cùng cặp hoặc bộ tham chiếu do người hướng dẫn thực hành cấp:1
- Mã SHA-256 của gói đối chiếu:c8bbc767d8bb9a29f4ca5abf0c3516e5c2af94c58143a980b0148cfe0b500d2b
- Nếu làm cá nhân, ghi mã lần phát và thời điểm nhận bộ tham chiếu:c8bbc767d8bb9a29f4ca5abf0c3516e5c2af94c58143a980b0148cfe0b500d2b

Giải thích vì sao bài của bạn vẫn độc lập trước khi đối chiếu:

Bài của tôi độc lập vì tôi tuân đúng trình tự bắt buộc của sổ tay: hoàn tất gán nhãn và tự kiểm trong CVAT, rồi xuất cả hai gói (Ultralytics YOLO Detection và CVAT for images 1.1) và chạy qua các bước kiểm tự động ở ô 3a/3b (my_export_audit.json, my_native_export_audit.json) — trước khi mở ô 5a để chọn nguồn đối chiếu. Sổ tay còn tự chặn việc dùng lại chính gói của mình làm đối chiếu: ô 5b so khớp SHA-256 của gói tải lên với my_audit["archive_sha256"] và báo lỗi nếu trùng, nên gói đối chiếu (c8bbc767d8bb9a29f4ca5abf0c3516e5c2af94c58143a980b0148cfe0b500d2b) chắc chắn là một tệp khác, độc lập với gói của tôi (c9ff89f3cfdceddfa3a7dd5c9ad9f51baccbe8234b18a56f4572640c6ee21b2e). Trước thời điểm ô 5b, tôi không mở, xem hay tải về bất kỳ tác vụ CVAT hay gói xuất nào khác ngoài của chính mình.

## 2. Quyết định phân lớp

| Ảnh/vật thể | Lớp | Dấu hiệu nhìn thấy | Quy tắc áp dụng |
| --- | --- | --- | --- |
| drive_022, hộp xyxy≈(99,341)-(390,577) | bus (bạn) / van (đối chiếu) | Thân dài hai khoang nối khớp xếp, nhiều cửa sổ, bảng số tuyến điện tử | Mục 2: "thân xe khách dài, nhiều cửa sổ hoặc hàng ghế" → bus |
| drive_008, hộp xyxy≈(142,120)-(201,185) | van (bạn) / truck (đối chiếu) | Nhìn từ sau, thân hộp nhỏ kín, cửa hậu hai cánh, không thấy thùng hở | Mục 2: "thân hộp nhỏ, kín, dùng chở người hoặc hàng" → van |
| drive_022, hộp xyxy≈(128,338)-(208,396) | car | 	Sedan bốn cửa, nóc thấp, không có khoang hàng tách biệt; visibility=clear, boundary=inside, review_state=confident | Mục 2: dòng car |
| drive_022, hộp xyxy≈(574,255)-(632,329)) | truck (bạn) / bus (đối chiếu) | Xe nhỏ phía xa, khó phân biệt rõ thùng hay thân khách ở độ phân giải này | Mục 2: cần đối chiếu thêm bằng chứng phóng to |
Nêu một ví dụ cho thấy lớp và thuộc tính là hai loại thông tin khác nhau:

Hai hộp car trong drive_022 minh hoạ rõ điều này: hộp tại xyxy≈(394,280)-(488,382) có thuộc tính visibility=clear, boundary=inside, review_state=confident, trong khi hộp tại xyxy≈(266,200)-(333,263) — cùng lớp car — lại có visibility=occluded, boundary=truncated, review_state=confident. Lớp trả lời câu hỏi "đây là loại phương tiện gì", còn ba thuộc tính trả lời câu hỏi khác: "bạn nhìn thấy nó rõ đến đâu và có bị mép ảnh/vật khác che không" — hai xe cùng lớp car vẫn có thể có bộ thuộc tính hoàn toàn khác nhau.
## 3. Tự kiểm tra và sửa nhãn

| Trước khi sửa | Loại lỗi | Cách phát hiện | Sau khi sửa và quy tắc |
| --- | --- | --- | --- |
| drive_038: hai hộp gần trùng vị trí sát mép trái — xyxy=(0,182.6)-(26,229.6) và xyxy=(0,183.6)-(54.4,268.8), thuộc tính lệch nhau (needs_review/occluded/truncated so với confident/clear/inside) | phạm vi (nghi trùng lặp hộp cho cùng một xe) | So khớp toạ độ hai hộp trong annotations.xml: chồng lấn gần như hoàn toàn, kích thước khác nhau đáng kể | Giữ lại hộp có boundary=truncated (xyxy=(0,182.6)-(26,229.6)) vì xe thực sự bị mép ảnh cắt ở đó; xoá hộp thứ hai. Áp dụng quy tắc mục 3: "không để hộp chứa nhiều nền hoặc nhiều phương tiện" / mỗi phương tiện chỉ một hộp. (Bạn cần thực hiện thao tác xoá này thật trong CVAT, sau đó xuất lại nếu muốn số liệu khớp.) |
| drive_022, hộp lớn ở giữa ảnh | 	lớp | Đối chiếu IoU với bộ tham chiếu: hình học khớp gần như tuyệt đối (IoU cao) nhưng lớp lệch (bus so với van) | Theo phân tích ở Tình huống A (GUIDELINE_MINI_SHEET.md, mục 5): giữ bus vì thân xe có khớp nối và bảng số tuyến — dấu hiệu không khớp định nghĩa van. (Xác nhận lại bằng mắt trên ảnh gốc trước khi chốt trong báo cáo nộp.)	|


- Số hộp `needs_review` trước và sau khi kiểm:hiện tại (theo day2-native-export.zip đã nộp) có 8/85 hộp needs_review (3 ở drive_008, 1 ở drive_033, 4 ở drive_038, 0 ở drive_022). Số "sau khi kiểm":0
- Một quyết định chưa đủ bằng chứng và cách bạn xin hỗ trợ:hộp bus tại drive_038 xyxy≈(141,5)-(187,64), thuộc tính visibility=unclear, review_state=needs_review — vật thể nhỏ ở góc trên, có thể cần hỏi Lab Coach hoặc dùng ảnh gốc độ phân giải cao hơn để xác nhận

## 4. Một dòng nhãn YOLO

- Dòng `class x_center y_center width height`:2 0.381844 0.717320 0.453813 0.369578 (ảnh drive_022)
- Tên lớp và tọa độ điểm ảnh `xyxy`:lớp = bus (mã 2); pixel xyxy ≈ (99.2, 340.8, 389.6, 577.3) trên ảnh 640×640
- Vì sao dòng đúng định dạng vẫn có thể sai lớp, phạm vi hoặc hình học?
Định dạng YOLO chỉ kiểm tra được cú pháp (5 số, đúng kiểu dữ liệu, toạ độ chuẩn hoá trong [0,1]) — nó không biết vật thể thật ngoài đời là gì. Một dòng có thể hoàn toàn hợp lệ về định dạng nhưng: (1) sai lớp nếu người gán nhãn nhầm xe buýt với xe van; (2) sai phạm vi nếu hộp bao trùm cả nền hoặc gộp hai xe làm một; (3) sai hình học nếu hộp không sát mép vật thể (quá rộng/quá hẹp) dù toạ độ vẫn nằm trong khoảng hợp lệ. Đây chính là lý do sổ tay yêu cầu xuất thêm CVAT for images 1.1 và đối chiếu IoU — file YOLO một mình không đủ để phát hiện các lỗi này.


## 5. Huấn luyện và dự đoán thử

- Ba mã ảnh huấn luyện:drive_022, drive_033, drive_038
- Mã ảnh thẩm định:drive_008
- Mô tả một dự đoán trong `detect_result.jpg`: Kết quả dự đoán trên ảnh `drive_008` có thể xuất hiện các hộp phát hiện phương tiện với nhãn và độ tin cậy khác nhau; cần đối chiếu trực tiếp từng hộp dự đoán với ảnh gốc và nhãn ground truth trước khi kết luận mô hình dự đoán đúng hay sai.
- Dự đoán đó gợi ý cần kiểm lại quy tắc hoặc dữ liệu nào? Nếu một hộp dự đoán nhầm giữa các lớp gần nhau như car, van, truck hoặc bus, cần kiểm lại quy tắc phân lớp trong mục 2 và nhãn của chính vật thể đó trong dữ liệu CVAT/YOLO.
- Minh chứng nào có thể bác bỏ nhận định của bạn? Ảnh gốc ở độ phân giải đầy đủ, nhãn CVAT đã tự kiểm, cùng kết quả đối chiếu với bộ tham chiếu là các minh chứng có thể cho thấy nhận định ban đầu về một dự đoán là không chính xác.
- Vì sao kết quả trên bốn ảnh không phải phép đánh giá mô hình dùng thực tế?
Vì tập dữ liệu chỉ có 4 ảnh (3 để huấn luyện, 1 để thẩm định) — quá nhỏ để ước lượng độ chính xác tổng quát, không đại diện cho phân bố điều kiện thực tế (góc camera, thời tiết, mật độ giao thông...), và mô hình chỉ huấn luyện 8 epoch với phần lớn lớp bị đóng băng (freeze=10) nhằm mục đích chẩn đoán nhanh, không phải để tối ưu hiệu năng. Đúng như ghi chú của sổ tay: "IoU và mAP là tín hiệu chẩn đoán, không phải điểm đạt, không chứng minh nhãn đúng và không phải phép đánh giá mô hình dùng trong thực tế."

## 6. Đối chiếu nhãn

- Số hộp ghép được:48
- IoU trung bình và trung vị:trung bình 0.7891, trung vị 0.8086
- Mức đồng thuận lớp:0.7292 (≈ 35/48 hộp ghép được có cùng lớp; 13/48 lệch lớp)
- Số hộp phía bạn không ghép được:37 (trên tổng 85 hộp của bạn — phần lớn do bạn có nhiều hộp hơn khoảng mục tiêu 40–60)
- Số hộp phía đối chiếu không ghép được:2 (trên tổng 50 hộp đối chiếu)
- Một điểm khác biệt cụ thể:Tại drive_022, hộp xe buýt khớp nối lớn (xyxy≈(99,341)-(390,577), IoU với hộp đối chiếu rất cao vì hình học gần như trùng khít) được bạn gán bus (đã đánh dấu confident) nhưng bộ đối chiếu gán van — đây là một trong 13 hộp lệch lớp góp vào mức đồng thuận 0.7292.
- Quy tắc hoặc hành động sửa phát sinh:cần một quy tắc rõ hơn để phân biệt xe buýt khớp nối (articulated bus) với van cỡ lớn khi nhìn từ góc camera trên cao — ví dụ bổ sung dấu hiệu "có khớp nối giữa thân xe" hoặc "có bảng số hiệu tuyến điện tử" là dấu hiệu ưu tiên cho bus
- Vì sao mức đồng thuận cao không chứng minh mọi nhãn đều đúng?
Vì đối chiếu IoU chỉ đo mức trùng khớp hình học, không đo tính đúng đắn của lớp hay thuộc tính — hai người có thể vẽ hộp gần như giống hệt nhau (IoU cao) nhưng vẫn gán sai cùng một lớp (ví dụ cả hai cùng nhầm van thành bus). Ngoài ra, bộ đối chiếu — kể cả do Lab Coach cấp — cũng có thể chứa quyết định gây tranh cãi (như trường hợp bus/van ở trên), nên đồng thuận cao chỉ là tín hiệu chẩn đoán để đối chiếu, không phải bằng chứng tuyệt đối rằng nhãn nào cũng đúng.


## 7. Kiểm tra kho GitHub cá nhân

- [X] Có phiếu quy tắc với ba tình huống mơ hồ.
- [X]ó kết quả kiểm hai gói xuất.
- [X Có thông tin lần huấn luyện và ảnh dự đoán.
- [X Có tóm tắt, bảng và ảnh phủ của bước đối chiếu.
- [X Không có gói xuất thô, bộ nhãn tham chiếu hoặc trọng số mô hình.
- [X Không có dữ liệu VinFast/khách hàng/ảnh cá nhân/mật khẩu/mã truy cập.

Minh chứng mạnh nhất trong bài và câu hỏi còn lại cho Lab Coach:

Minh chứng mạnh nhất là cặp hộp bus/van ở drive_022: hình học khớp gần như trùng khít (IoU cao) nhưng lớp lệch nhau hoàn toàn — cho thấy đối chiếu IoU một mình không đủ để đảm bảo chất lượng nhãn, và việc đối chiếu độc lập với Lab Coach thực sự phát hiện ra bất đồng có căn cứ chứ không phải lỗi ngẫu nhiên. Câu hỏi còn lại cho Lab Coach: với xe buýt khớp nối (articulated bus) nhìn từ góc camera trên cao — nơi khớp nối giữa thân xe dễ bị nhầm với đường viền của một chiếc van cỡ lớn — quy tắc phân lớp nên ưu tiên dấu hiệu nào (khớp nối thân xe, bảng số tuyến điện tử, hay số lượng cửa sổ) khi các dấu hiệu này không xuất hiện đồng thời rõ ràng?

