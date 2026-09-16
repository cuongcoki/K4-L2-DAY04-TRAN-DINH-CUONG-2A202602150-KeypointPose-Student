# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: Trần Đình Cường (theo tên thư mục bài nộp)  
Hình thức: Làm cá nhân, không có nhóm.  
Ngày làm bài: 16/09/2026

> Báo cáo được lập từ dữ liệu hiện có. Các mục **[CẦN BỔ SUNG]** chưa có đủ bằng chứng hoặc cần người gán xác nhận; chưa phải bản hoàn tất để nộp. Số liệu nhãn lấy từ `outputs/eval_vs_gold.json` và `reports/visibility_report.md`. Số liệu model chép từ phần đánh giá cuối trong file Word `reports/chạy colab và log.docx`; file `outputs/eval_model.json` chưa có trong bản dự án hiện tại.

## 1. Nhãn của tôi

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 27 |
| v=2 / v=1 / v=0 | 336 / 99 / 24 |
| Thời gian trung bình mỗi ảnh | **[CẦN BỔ SUNG tổng thời gian gán / 20]** |

Nguồn: [visibility_report.md](visibility_report.md). Trung bình mỗi người có 16.11 khớp với `v > 0`.

Ba khớp có `%v=1` cao nhất:

1. `left_ear`: 48% (13/27 skeleton).
2. `right_ear`: 44% (12/27 skeleton).
3. `left_eye`: 33% (9/27 skeleton), đồng hạng với `left_wrist`: 33% (9/27 skeleton).

**Chúng có đúng là những khớp khó gán nhất không?**

**[CẦN BỔ SUNG trải nghiệm thực tế và ảnh minh họa của người gán.]** Bảng thống kê cho thấy hai tai có tỷ lệ được gán cờ bị che cao nhất; riêng tỷ lệ này chưa chứng minh đó là các khớp khó xác định vị trí nhất. Cần phân biệt việc khớp hay bị che với việc khó xác định vị trí giải phẫu, và đối chiếu ảnh cụ thể trước khi kết luận.

## 2. Chấm với gold

Tôi chưa thực hiện rework. Kết quả trong [eval_vs_gold.json](../outputs/eval_vs_gold.json), trùng với lần chấm trong log đã cung cấp, là kết quả trước rework. Chưa có lần chấm sau rework để so sánh.

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | --- |
| OKS trung bình | 0.9582 | Chưa thực hiện |
| OKS@0.50 | 0.931 | Chưa thực hiện |
| OKS@0.75 | 0.931 | Chưa thực hiện |
| Lỗi `dao_trai_phai` | 0 | Chưa thực hiện |
| Lỗi `nham_nguoi` | 0 | Chưa thực hiện |
| Lỗi `xoa_khop_bi_che` | 0 | Chưa thực hiện |

Đếm các finding trong JSON: thiếu người 2; lệch nhẹ 1; cờ khác gold 56; gold không gán khớp 66. Gold có 29 người, ghép được 27, thiếu 2 và thừa 0. Theo cách tính trong script, OKS trung bình tính trên người ghép được; vì vậy 0.9582 không có nghĩa là đã gán đủ mọi người. Log xếp kết quả ở mức “Xuất sắc”, nhưng vẫn chỉ ra người bị thiếu.

**Tôi đã sửa gì giữa hai lần chạy:**

Chưa sửa nhãn sau lần chấm gold, nên chưa có thay đổi giữa hai lần chạy. Những mục dưới đây là phát hiện cần xử lý, chưa phải các sửa đổi đã thực hiện:

- `train_13.jpg`: thiếu người #1 và #2 theo thứ tự gold; mỗi người có OKS 0 do không ghép được skeleton.
- `train_04.jpg`: người #2 trong nhãn của tôi (ghép với người #1 của gold), `left_wrist` lệch 40 px, tương đương 1.1 lần bán kính dung sai; được phân loại `lech_nhe`.

**Lỗi đảo trái/phải xảy ra ở ảnh nào?**

Không có finding `dao_trai_phai` trong kết quả chấm gold của toàn bộ 20 ảnh. Tuy nhiên, log kiểm định dạng vẫn cảnh báo vai và hông của người #1 ở `train_02.jpg` và `train_16.jpg` có chiều trái/phải ngược với hai mắt. Đây là dấu hiệu cần soi ảnh, chưa đủ để kết luận chắc chắn có đảo khớp. **[CẦN BỔ SUNG kết quả kiểm tra trực quan và nguyên nhân nếu xác nhận có lỗi.]**

Đối chiếu [REVIEWER_CHECKLIST.md](REVIEWER_CHECKLIST.md): log ghi “ĐẠT định dạng” nhưng còn 6 cảnh báo, gồm 4 cảnh báo trái/phải nói trên và 2 cảnh báo ở `train_10.jpg`, `train_11.jpg` (người #1 có 4 khớp `v=0` dù bộ kiểm tra nhận định người nằm giữa ảnh). Do đó chưa thể đánh dấu đã đạt toàn bộ checklist, đặc biệt các mục đủ người và dùng đúng visibility.

## 3. Kiểm chéo

Tôi làm bài cá nhân, không có bạn cùng nhóm và chưa thực hiện kiểm chéo với người khác.

File [visibility_compare.md](visibility_compare.md) hiện so nhãn của tôi với `../ban_cung_nhom/dataset/labels/train`, nhưng phía đối chiếu có **0 skeleton**. Các giá trị 0% ở cột đối chiếu không đại diện cho một bài gán nhãn hợp lệ; không dùng các độ lệch trong file này để kết luận bất đồng guideline.

**Luật mới đã bổ sung vào `GUIDELINE_MINI.md` sau khi thống nhất:**

Không có luật mới từ hoạt động thống nhất nhóm vì bài được thực hiện cá nhân. [GUIDELINE_MINI.md](../GUIDELINE_MINI.md) hiện vẫn còn trống phần ba ca mơ hồ; các quyết định gán nhãn cá nhân cần được bổ sung từ trải nghiệm thực tế.

## 4. Model

Nguồn: phần 2 và phần 4 của [chạy colab và log.docx](chạy%20colab%20và%20log.docx). Bảng dùng kết quả đánh giá riêng trên tập test ở phần 4, không dùng giá trị khoảng 0.702 từ bước validation ngay sau huấn luyện.

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | 0.8450 | 0.8450 | +0.0000 |
| pose_mAP50-95 | 0.6853 | 0.6908 | +0.0055 |
| pose_precision | 0.9734 | 0.9792 | +0.0058 |
| pose_recall | 0.8462 | 0.8462 | +0.0000 |
| box_mAP50-95 | 0.8119 | 0.8041 | -0.0078 |

Thông tin lần chạy trong Word: 20 ảnh train/27 skeleton; 10 ảnh test/13 skeleton; GPU Tesla T4; `imgsz=640`, `batch=8`, `epochs=80`, `patience=30`, `fliplr=0.5`, `seed=20260915`. Huấn luyện dừng sớm sau 39 epoch, checkpoint tốt nhất ở epoch 9. Optimizer thực tế là AdamW với learning rate 0.002 do `optimizer=auto` lựa chọn.

Tập 10 ảnh test cũng được dùng làm validation trong huấn luyện và chọn checkpoint. Vì vậy các số liệu dưới đây là quan sát trên tập của bài thực hành, chưa phải bằng chứng về chất lượng trên một tập đánh giá độc lập mới.

### Trả lời năm câu hỏi ở cuối notebook

1. **`pose_mAP50-95` thay đổi bao nhiêu?**

   Chỉ số tăng từ 0.6853 lên 0.6908, chênh +0.0055, tương đương +0.55 điểm phần trăm. `pose_precision` tăng +0.0058, còn `pose_mAP50` và `pose_recall` giữ nguyên theo độ chính xác hiển thị trong log. `box_mAP50-95` giảm 0.0078. Kết quả cho thấy mức cải thiện nhỏ về định vị pose trên tập đánh giá này, nhưng chưa đủ để xác định model đã học thêm đặc điểm nào ngoài COCO hoặc quy mức giảm của box cho lỗi nhãn cụ thể.

2. **`box_mAP` và `pose_mAP` chênh nhau bao nhiêu? Model tìm người hay tìm khớp dễ hơn?**

   Ở mAP50-95, baseline có chênh lệch box trừ pose là `0.8119 - 0.6853 = 0.1266`; sau fine-tune là `0.8041 - 0.6908 = 0.1133`. Ở mAP50, chênh lệch tương ứng là `0.9785 - 0.8450 = 0.1335` và `0.9600 - 0.8450 = 0.1150`. Box đạt điểm cao hơn pose trong lần đánh giá này, phù hợp với việc khoanh vùng một người ít đòi hỏi định vị chi tiết hơn xác định nhiều khớp. Tuy nhiên, box được chấm bằng IoU và pose bằng OKS nên không diễn giải hiệu số này như hai phép đo hoàn toàn giống nhau.

3. **Một ảnh test model đoán sai và loại lỗi:**

   **[CẦN BỔ SUNG ảnh dự đoán test để soi khớp và xác định một trong bốn loại: lệch nhẹ / đảo trái-phải / nhầm người / trượt hẳn.]** Word chỉ có log số người dự đoán và đường dẫn `/content/Day4-Lab/outputs/runs/predictions/test`; không có ảnh nhúng. Chỉ từ số người dự đoán chưa thể xác định lỗi pose của ảnh nào.

4. **Ảnh có OKS thấp nhất giữa nhãn của tôi và model; ai đúng?**

   Trong bảng đối chiếu của Word, skeleton có OKS thấp nhất nằm ở `train_15`, đạt 0.63. Bảng không ghi người thứ mấy; cùng ảnh còn một skeleton đạt 0.895. Đối chiếu gold, hai người được ghép ở `train_15.jpg` có OKS lần lượt 0.9156 và 0.9561, cho thấy nhãn của tôi khá gần gold trên các khớp được chấm. Tuy nhiên chưa thể xác định ai đúng ở từng khớp bất đồng nếu chưa có ảnh overlay của model, nhất là các khớp gold bỏ qua. **[CẦN BỔ SUNG ảnh đối chiếu và keypoint cụ thể.]** Hai ca khác cần xem là `train_06` (0.685) và `train_03` (0.713).

5. **Ảnh tôi gán tệ nhất có cũng là ảnh model đoán tệ nhất không?**

   Nếu xét thiếu người, lỗi nổi bật trong bài gán là `train_13.jpg`: thiếu 2 người so với gold. Model dự đoán 3 người, nhãn của tôi có 1 người; skeleton được đem so có OKS 0.984. Trong khi đó, ca bất đồng tọa độ thấp nhất giữa model và nhãn của tôi là `train_15` với OKS 0.63. Hai trường hợp này không trùng nhau, nhưng “bất đồng với nhãn của tôi” chưa đồng nghĩa “model sai nhất”. Chưa có kết quả model so với gold theo từng ảnh để xếp hạng ảnh model đoán tệ nhất; không thể kết luận chắc chắn hai bên cùng sai vì ảnh khó.

## 5. Một rule evidence bạn đã dùng

**[CẦN BỔ SUNG từ người gán: ảnh core, người thứ mấy, keypoint, bằng chứng nhìn thấy, cờ đã chọn và lý do.]** Chưa có ca thực tế được ghi trong `GUIDELINE_MINI.md`, nên chưa thể viết một quyết định cá nhân như thể đã thực hiện.

Nguyên tắc có sẵn trong guideline: khớp bị che nhưng còn trong khung dùng `v=1` và vẫn đặt chấm; khớp thực sự ra ngoài mép ảnh dùng `v=0`. Khi bổ sung ca cụ thể, cần nêu phần cơ thể liền kề hoặc vật che làm căn cứ, rồi giải thích vì sao vị trí khớp còn trong hay đã ra ngoài khung, trong 3–5 câu.
