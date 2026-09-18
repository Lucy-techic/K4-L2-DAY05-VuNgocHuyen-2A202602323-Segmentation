
- Mã học viên theo lớp: 2A202602323 (Vũ Ngọc Huyền)
- Ngày / CVAT local: 17-18/09/2026, CVAT local (localhost:8080, chạy qua Docker trong WSL)
- Công cụ đã dùng: Brush, Polygon

## 1. Bài đã nộp

| Task | File ZIP đúng tên | Hoàn thành mấy ảnh | Điểm tối đa (coach chấm sau) |
| --- | --- | ---: | ---: |
| easy_semantic | easy_semantic.zip | 3 / 3 | 20 |
| medium_instance | medium_instance.zip | 3 / 3 | 32 |
| hard_panoptic | hard_panoptic.zip | 2 / 2 | 30 |
| cp1_holes | cp1_holes.zip | 1 / 1 | 3 |
| cp2_slice | cp2_slice.zip | 1 / 1 | 3 |
| cp5_occlusion | cp5_occlusion.zip | 1 / 1 | 3 |
| cp3_thin | cp3_thin.zip | 1 / 1 | 3 |
| cp4_curb | cp4_curb.zip | 1 / 1 | 3 |
| cp6_coverage | cp6_coverage.zip | 1 / 1 | 3 |
| **Tổng tối đa** | | | **100** |

Không tự điền điểm, chờ coach chấm.

## 2. Một quyết định trước khi dùng gợi ý

- Ảnh, vị trí và object Medium đầu tiên tự vẽ: ảnh `000000181542.jpg` (task medium_instance), object là cô gái mặc áo dài trắng đi bộ giữa đường, ở khoảng giữa khung hình.
- Class và quy tắc tôi dùng để chọn biên: class `person`. Tôi vẽ theo đúng viền cơ thể nhìn thấy (đầu, thân, tay, chân, tà áo dài), không mở rộng ra phần bóng đổ trên đường và không đoán phần cơ thể bị các phương tiện phía trước che khuất.
- Nếu dùng gợi ý sau đó: không sử dụng công cụ gợi ý tự động (Intelligent Scissors/SAM) trong suốt quá trình làm bài; toàn bộ mask đều tự vẽ bằng Polygon/Brush.
- Nếu không dùng gợi ý: không dùng. Với các object person tiếp theo trong cùng ảnh (2 người lái xe máy phía sau), tôi tiếp tục tách riêng phần cơ thể người khỏi phần xe máy, dù 2 mask có chồng lấn ở vị trí người ngồi trên yên xe — đây là chồng lấn tự nhiên giữa 2 instance khác nhau, không phải lỗi.

## 3. Một lỗi tôi tìm thấy và sửa

- Task/ảnh/vùng: `easy_semantic`, ảnh `7ee6d192-89e2408b.jpg`, vùng ranh giới giữa sky và vegetation ở phần đồi bên phải.
- Lỗi thuộc loại: biên (khoảng hở giữa 2 mask liền kề).
- Bằng chứng tôi nhìn thấy: sau khi vẽ xong sky (Polygon) và vegetation (Brush), khi zoom lại kiểm tra phát hiện một khoảng hở hình tam giác nhỏ giữa 2 đường viền — phần đồi có ánh đèn xe ở giữa không thuộc mask nào cả, để lộ pixel gốc chưa được gán class.
- Quy tắc và hành động sửa: theo quy tắc "không để trống vùng thuộc class của task", tôi dùng Brush (label vegetation) tô lấp trực tiếp vào khoảng hở đó cho tới khi đường viền vegetation trùng khít với đường viền sky đã vẽ trước, không còn khoảng trống nhìn thấy.
- Sau sửa đã Save và export lại chưa? Đã Save trong CVAT trước khi Export job dataset (Segmentation mask 1.1) và copy vào submissions/easy_semantic.zip.

Nếu đã xem điểm tự đánh giá trên GitHub Actions hoặc chạy scorer: chưa có — chưa nhận được ground truth/chạy Actions self-check tại thời điểm nộp báo cáo này.

## 4. Ba ca chưa chắc hoặc đã cân nhắc

| Ảnh/vị trí | Hai cách hiểu có thể | Quy tắc/chứng cứ | Quyết định hoặc câu hỏi cho coach |
| --- | --- | --- | --- |
| 1 | `cp6_coverage`, ảnh `7daa6479-67988f3f.jpg`: xe bus trắng lớn chiếm phần lớn khung hình | (a) Bỏ qua, không gán label nào cho phần xe bus vì không có class "bus"/"truck" trong 7 label được cấp cho task này; (b) tạm gán label "car" dù không chính xác về loại phương tiện | 7 label của cp6_coverage chỉ gồm road, sidewalk, building, vegetation, sky, car, person — không có bus/truck | Tôi chọn (a): không gán label nào cho phần thân xe bus, để tránh gán sai class. Xin coach xác nhận cách xử lý đúng cho case phương tiện không có label phù hợp trong bộ class của task. |
| 2 | `cp4_curb`, ảnh `7d83710e-4697c3b2.jpg`: dải đất/cỏ khô có cây và túi rác nằm giữa road và sidewalk (không chạm trực tiếp nhau) | (a) Tính dải đất là một phần của sidewalk (vì nằm trong khu vực người đi bộ, sát vỉa hè); (b) không gán class nào cho dải đất vì không phải bê tông cứng | Chỉ có 2 label road/sidewalk được cấp cho task này, không có class riêng cho đất/cỏ | Tôi chọn (a): gộp dải đất vào sidewalk vì đây là phần đất công thuộc khu vực vỉa hè, không phải mặt đường xe chạy. Xin coach xác nhận cách xử lý dải phân cách đất khi chỉ có 2 label road/sidewalk. |
| 3 | `cp5_occlusion`, ảnh `000000336232.jpg`: các xe đậu sát nhau trong hàng xe bên trái, một số xe bị xe phía trước che khuất một phần (chỉ thấy đầu hoặc đuôi xe) | (a) Vẽ 1 mask duy nhất cho phần nhìn thấy của xe bị che (coi là 1 instance); (b) không vẽ phần xe bị che nếu không chắc đó là 1 xe hoàn chỉnh | Theo quy tắc occlusion: vật bị che vẫn là 1 instance nếu phần nhìn thấy thuộc cùng 1 vật thể thật | Tôi chọn (a): vẽ mask cho phần thân xe nhìn thấy được, coi là 1 object car duy nhất, không đoán phần bị che khuất phía sau xe khác. |
EOF
