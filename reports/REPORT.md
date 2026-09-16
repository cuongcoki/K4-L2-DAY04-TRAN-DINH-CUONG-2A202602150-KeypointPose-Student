# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: Trần Đình Cường  
Hình thức: Làm cá nhân  
Ngày: 16/09/2026

## 1. Nhãn của tôi

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 27 |
| v=2 / v=1 / v=0 | 336 / 99 / 24 |
| Thời gian trung bình mỗi ảnh | Không đo trong quá trình gán |
| Trung bình số khớp có `v > 0` mỗi người | 16.11 |

Ba khớp có `%v=1` cao nhất:

1. `left_ear`: 48% (13/27 skeleton).
2. `right_ear`: 44% (12/27 skeleton).
3. `left_eye`: 33% (9/27 skeleton), đồng hạng với `left_wrist`: 33% (9/27 skeleton).

Hai tai đúng là nhóm khớp khó gán vì thường bị tóc, mũ hoặc góc quay của đầu che khuất; số liệu cũng cho thấy `left_ear` và `right_ear` có tỷ lệ `v=1` cao nhất. `left_wrist` cũng khó vì bàn tay có kích thước nhỏ và cổ tay dễ bị vật thể hoặc thân người che. Đây là khó khăn về khả năng quan sát và xác định vị trí giải phẫu; khi khớp còn trong khung nhưng bị che, tôi vẫn đặt điểm ước lượng và dùng `v=1`.

## 2. Chấm với gold

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | 0.9582 | Không áp dụng |
| OKS@0.50 | 0.931 | Không áp dụng |
| OKS@0.75 | 0.931 | Không áp dụng |
| Lỗi `dao_trai_phai` | 0 | Không áp dụng |
| Lỗi `nham_nguoi` | 0 | Không áp dụng |
| Lỗi `xoa_khop_bi_che` | 0 | Không áp dụng |

Kết quả được xếp mức **Xuất sắc**. Gold có 29 người, nhãn của tôi ghép được 27 người, thiếu 2 người và không thừa người. Danh sách finding gồm 2 lỗi thiếu hẳn một người, 1 lỗi lệch nhẹ, 56 trường hợp cờ visibility khác gold nhưng vị trí vẫn đúng, và 66 trường hợp gold đặt `v=0` tại khớp tôi có gán. Hai nhóm khác biệt visibility không làm giảm OKS.

**Tôi đã sửa gì giữa hai lần chạy:**

Tôi không thực hiện rework nên báo cáo sử dụng kết quả của lần chấm gold đầu tiên. Hai skeleton được công cụ ưu tiên đề nghị sửa đều nằm ở `train_13.jpg`: người #1 và người #2 của gold không có skeleton tương ứng trong nhãn của tôi. Ngoài ra, `left_wrist` của người #2 trong nhãn tại `train_04.jpg` lệch 40 px, tương đương 1.1 lần bán kính dung sai, và được phân loại là lỗi lệch nhẹ.

**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào?**

Không có lỗi `dao_trai_phai` trong kết quả chấm gold của toàn bộ 20 ảnh. Bộ kiểm tra hình học từng phát cảnh báo tại `train_02.jpg` và `train_16.jpg` vì hướng vai, hông khác hướng hai mắt, nhưng đây là cảnh báo heuristic theo tư thế và không được bộ chấm gold xác nhận là lỗi đảo trái/phải.

## 3. Kiểm chéo

Bài được thực hiện cá nhân nên phần kiểm chéo với bạn cùng nhóm không áp dụng. File `visibility_compare.md` đối chiếu với một thư mục có 0 skeleton, vì vậy các tỷ lệ 0% ở phía đối chiếu không được sử dụng làm kết quả đánh giá.

Quy tắc được sử dụng nhất quán trong bài là: khớp bị che nhưng vẫn nằm trong khung ảnh được đặt điểm ước lượng và gán `v=1`; chỉ gán `v=0` khi khớp thực sự nằm ngoài mép ảnh. Quy tắc này có thể kiểm chứng trực tiếp bằng vị trí của phần cơ thể liền kề và mép ảnh.

## 4. Model

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | 0.8450 | 0.8450 | +0.0000 |
| pose_mAP50-95 | 0.6853 | 0.6908 | +0.0055 |
| pose_precision | 0.9734 | 0.9792 | +0.0058 |
| pose_recall | 0.8462 | 0.8462 | +0.0000 |
| box_mAP50-95 | 0.8119 | 0.8041 | -0.0078 |

Model được fine-tune trên 20 ảnh train với 27 skeleton và đánh giá trên 10 ảnh test có 13 skeleton. Cấu hình chính gồm `imgsz=640`, `batch=8`, tối đa 80 epoch, `patience=30`, `fliplr=0.5` và `seed=20260915`. Quá trình huấn luyện dừng sớm sau 39 epoch; checkpoint tốt nhất được ghi nhận ở epoch 9. Optimizer thực tế là AdamW với learning rate 0.002 do chế độ `optimizer=auto` lựa chọn.

### Trả lời năm câu hỏi ở cuối notebook

1. **`pose_mAP50-95` thay đổi bao nhiêu? Nếu nó giảm, 20 ảnh của bạn dạy được model điều gì mà COCO chưa dạy, và nó làm hỏng điều gì?**

   `pose_mAP50-95` tăng từ 0.6853 lên 0.6908, tức tăng 0.0055 hay 0.55 điểm phần trăm. Vì chỉ số không giảm, phần giải thích tình huống giảm không áp dụng. Kết quả cho thấy 20 ảnh giúp model cải thiện nhẹ khả năng định vị pose trên tập test: `pose_precision` tăng 0.0058, trong khi `pose_mAP50` và `pose_recall` giữ nguyên. Đổi lại, `box_mAP50-95` giảm 0.0078, cho thấy fine-tune trên tập nhỏ cải thiện pose rất ít nhưng làm khả năng định vị hộp người giảm nhẹ.

2. **`box_mAP` và `pose_mAP` chênh nhau bao nhiêu? Model tìm người dễ hơn hay tìm khớp dễ hơn? Vì sao?**

   Ở mAP50-95, độ chênh giữa box và pose là 0.1266 với model gốc (`0.8119 - 0.6853`) và 0.1133 sau fine-tune (`0.8041 - 0.6908`). Model tìm người dễ hơn tìm chính xác các khớp vì bounding box chỉ cần bao quanh cơ thể, còn pose phải định vị đồng thời 17 điểm, kể cả các điểm nhỏ hoặc bị che như tai và cổ tay. Box và pose dùng hai cách chấm khác nhau là IoU và OKS, nên độ chênh này được dùng để mô tả kết quả thực nghiệm thay vì coi là hai đại lượng hoàn toàn tương đương.

3. **Một ảnh test model đoán sai và loại lỗi:**

   Ở `test_02`, người rất nhỏ phía bên trái có confidence khoảng 0.31. Model tìm được một bounding box nhưng các keypoint co cụm ở vùng dưới của box và không tạo thành tư thế người hợp lý như người phía bên phải. Tôi xếp trường hợp này vào loại **trượt hẳn** vì sai khác nằm ở toàn bộ cấu trúc pose, không chỉ lệch nhẹ một khớp. Nguyên nhân chính là kích thước người quá nhỏ và phần cơ thể quan sát được rất ít.

4. **Ảnh nào có OKS thấp nhất giữa nhãn của bạn và model? Ai đúng, và bạn dựa vào đâu?**

   Skeleton có OKS thấp nhất giữa model và nhãn của tôi nằm ở `train_15`, đạt 0.63. Trên cùng ảnh, skeleton còn lại đạt 0.895. Khi đối chiếu với gold, hai skeleton trong nhãn của tôi đạt OKS lần lượt 0.9156 và 0.9561; vì vậy nhãn của tôi gần gold hơn kết quả model ở trường hợp 0.63. Tôi dựa vào điểm gold theo từng người thay vì chỉ dựa vào mức bất đồng giữa model và nhãn.

5. **Ảnh bạn gán tệ nhất có cũng là ảnh model đoán tệ nhất không? Nếu có, điều đó nói gì về bức ảnh đó?**

   Không. Lỗi lớn nhất của phần gán nhãn nằm ở `train_13.jpg`, nơi nhãn của tôi thiếu 2 người so với gold. Ca model bất đồng mạnh nhất với nhãn của tôi lại nằm ở `train_15`, với OKS 0.63. Điều này cho thấy lỗi gán thiếu người và lỗi dự đoán keypoint của model xuất hiện ở hai ảnh khác nhau; không thể quy cả hai cho cùng một bức ảnh khó.

## 5. Một rule evidence tôi đã dùng

Ở `train_10.jpg`, người #1, tôi gán `v=0` cho `left_knee` và các khớp chân nằm thấp hơn. Ảnh chỉ hiển thị người từ phần thân trên đến gần hông; phần chân tiếp tục ra ngoài mép dưới của ảnh. Vị trí hông còn có thể ước lượng từ thân người nên được đặt điểm với `v=1`, còn đầu gối không còn nằm trong khung và không có vị trí ảnh hợp lệ để đặt điểm. Vì vậy `left_knee` được gán `v=0`, đúng với quy tắc chỉ dùng `v=0` khi khớp ra ngoài mép ảnh.
