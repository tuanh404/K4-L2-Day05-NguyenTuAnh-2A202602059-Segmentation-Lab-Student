# Báo cáo Day 5

- Mã học viên theo lớp: 2A202602059
- Ngày / CVAT: 17–18/09/2026 · CVAT online của lớp
- Công cụ đã dùng: CVAT; Automatic Annotation/model AI để tạo mask thử nghiệm; kiểm tra trực quan trên CVAT; Python scorer của repo để đối chiếu với ground truth.

## 1. Bài đã nộp

| Task | File ZIP đúng tên | Hoàn thành mấy ảnh | Điểm tối đa (coach chấm sau) |
| --- | --- | ---: | ---: |
| easy_semantic | `easy_semantic.zip` | 3 / 3 | 20 |
| medium_instance | `medium_instance.zip` | 3 / 3 | 32 |
| hard_panoptic | `hard_panoptic.zip` | 2 / 2 | 30 |
| cp1_holes | `cp1_holes.zip` | 1 / 1 | 3 |
| cp2_slice | `cp2_slice.zip` | 1 / 1 | 3 |
| cp5_occlusion | `cp5_occlusion.zip` | 1 / 1 | 3 |
| cp3_thin | `cp3_thin.zip` | 1 / 1 | 3 |
| cp4_curb | `cp4_curb.zip` | 1 / 1 | 3 |
| cp6_coverage | `cp6_coverage.zip` | 1 / 1 | 3 |
| **Tổng tối đa** | | | **100** |

Cả ba task Easy, Medium, Hard và sáu checkpoint đã được Save trên CVAT, export đúng format và đặt đúng tên trong thư mục `submissions/`. Script `inspect_submissions.py` xác nhận cả chín ZIP đều `[OK]` về tên, ảnh, class và cấu trúc export.

## 2. Một quyết định trước khi dùng gợi ý

- Ảnh, vị trí và object Medium đầu tiên tự vẽ: Tôi không ghi lại chính xác ảnh, vị trí và object đầu tiên trước khi thử gợi ý AI. Đây là thiếu sót trong quá trình ghi nhật ký và tôi không tự điền một object không thể xác minh.
- Class và quy tắc tôi dùng để chọn biên: Khi kiểm tra phương tiện, tôi dựa trên phần vật thể nhìn thấy, không tự đoán phần bị che. Hai phương tiện khác nhau phải là hai instance riêng dù đứng sát hoặc chạm nhau.
- Nếu dùng gợi ý sau đó: Tôi có thử Automatic Annotation/model AI để tạo mask ban đầu. Model nhận ra nhiều vùng phương tiện nhưng có trường hợp nối nhiều xe sát nhau thành một mask, đồng thời nhầm giữa `bicycle`, `motorcycle`, `car` và `truck`. Tôi giữ bản export này để đo kết quả thật của model thay vì ghi rằng đã sửa thủ công.

## 3. Một lỗi tôi tìm thấy và sửa

- Task/ảnh/vùng: `medium_instance`, đặc biệt các vùng có nhiều xe sát hoặc che nhau trong ba ảnh.
- Lỗi thuộc loại: Gộp-tách instance, sai class và thừa vật.
- Bằng chứng tôi nhìn thấy: Một số mask do AI tạo nối nhiều xe thành một object. Scorer ghi nhận 103 object dùng để chấm so với 71 object trong ground truth; kết quả TP 22, FP 81 và FN 49. Thống kê class cũng cho thấy bài có 14 `bicycle` trong khi ground truth có 0, và 19 `motorcycle` trong khi ground truth có 4.
- Quy tắc và hành động sửa: Quy tắc đúng là mỗi phương tiện vật lý phải có một mask riêng; một vật bị che nhưng vẫn là cùng một phương tiện thì giữ cùng instance. Trong lần nộp này tôi chỉ xác định và ghi lại lỗi để đánh giá khả năng của model AI, chưa tách hoặc gán lại toàn bộ mask thủ công.
- Sau sửa đã Save và export lại chưa? Chưa sửa lại lỗi gộp-tách trên ba tier. Ba ZIP Easy, Medium và Hard hiện tại là kết quả đã Save/export của lần thử model AI; sáu ZIP checkpoint được hoàn thành và export riêng sau đó.

Kết quả tự chấm thật bằng scorer và ground truth:

| Task | Chỉ số chính | Điểm tự chấm |
| --- | --- | ---: |
| easy_semantic | mIoU 0,655; coverage 98,2%; `sidewalk` IoU 0,191 | 11,3 / 20 |
| medium_instance | mean matched IoU 0,724; P@0.5 0,21; R@0.5 0,31 | 0,0 / 32 |
| hard_panoptic | PQ 0,254; SQ 0,427; RQ 0,338 | 3,6 / 30 |
| **Tổng ba tier** | | **14,9 / 82** |

Đây là kết quả tự kiểm của đầu ra model AI, không phải điểm cuối do coach xác nhận. Tôi không tự ghi PASS, top 3 hoặc bonus và không đưa ground truth vào fork.

## 4. Ba ca chưa chắc hoặc đã cân nhắc

| Ảnh/vị trí | Hai cách hiểu có thể | Quy tắc/chứng cứ | Quyết định hoặc câu hỏi cho coach |
| --- | --- | --- | --- |
| `medium_instance/000000181542.jpg`, các xe máy giao nhau quanh người đi bộ ở giữa và hai bên ảnh | Gộp các vùng xe đang chạm nhau thành một mask, hoặc tách theo từng xe | Có thể nhìn thấy các thân xe, bánh xe và người điều khiển khác nhau; phần vật thể bị người khác che không nên được tự đoán | Về nguyên tắc phải tách từng xe thành một instance. Kết quả AI có trường hợp nối liền nên đây là lỗi cần sửa nếu tiếp tục cải thiện bài. |
| `medium_instance/000000458325.jpg`, hai hàng ô tô đỗ sát nhau hai bên đường | Gán mọi vùng xe liền nhau thành một object, hoặc tạo một object cho từng ô tô nhìn thấy | Mỗi ô tô là một vật thể riêng; khoảng hở, kính, đèn và đường bao thân xe giúp phân biệt | Chọn tách từng ô tô. Với xe quá xa hoặc chỉ lộ một phần rất nhỏ, cần coach xác nhận ngưỡng tối thiểu để vẫn gán nhãn. |
| `hard_panoptic/000000350023.jpg`, các phương tiện rất nhỏ ở xa trên đại lộ | Gán nhãn tất cả phương tiện còn thấy được, hoặc bỏ qua vật quá nhỏ/khó xác định class | Khoảng cách làm mất chi tiết phân biệt `car`, `truck` và `motorcycle`; tự đoán class làm tăng false positive | Chỉ nên gán khi còn đủ dấu hiệu nhận diện class. Câu hỏi cho coach: có ngưỡng kích thước pixel hoặc mức chắc chắn tối thiểu cho phương tiện ở xa không? |
