# Visibility report

- Thư mục nhãn: `dataset\labels\train`
- 20 ảnh, 27 skeleton, trung bình 16.11 khớp có v > 0 mỗi người
- Tổng: v=2 336 | v=1 99 | v=0 24

So sánh với `..\ban_cung_nhom\dataset\labels\train` (0 skeleton).
Cột **lệch** là hiệu số phần trăm v=1 - chỗ nào lệch nhiều nhất là chỗ guideline chưa nói rõ.

| # | Khớp | %v=1 (bạn) | %v=1 (đối chiếu) | lệch |
| ---: | --- | ---: | ---: | ---: |
| 3 | left_ear | 48% | 0% | 48 |
| 4 | right_ear | 44% | 0% | 44 |
| 1 | left_eye | 33% | 0% | 33 |
| 9 | left_wrist | 33% | 0% | 33 |
| 2 | right_eye | 30% | 0% | 30 |
| 0 | nose | 22% | 0% | 22 |
| 10 | right_wrist | 22% | 0% | 22 |
| 11 | left_hip | 22% | 0% | 22 |
| 8 | right_elbow | 19% | 0% | 19 |
| 12 | right_hip | 19% | 0% | 19 |
| 16 | right_ankle | 19% | 0% | 19 |
| 7 | left_elbow | 15% | 0% | 15 |
| 14 | right_knee | 15% | 0% | 15 |
| 15 | left_ankle | 11% | 0% | 11 |
| 6 | right_shoulder | 7% | 0% | 7 |
| 13 | left_knee | 7% | 0% | 7 |
| 5 | left_shoulder | 0% | 0% | 0 |

## Đọc bảng này thế nào

1. Khớp nào có **%v=1 cao**: khớp hay bị che. Cổ tay và hông thường là hai vị trí cần xem lại guideline trước khi kết luận.
2. Khớp nào có **v=0 cao bất thường**: mọi người đang dùng Outside ở chỗ đáng lẽ là Occluded. Đó là lỗi số 3 của slide 46, và nó xoá thẳng khớp đó khỏi bảng điểm OKS.
3. Khi so hai người: **lệch lớn = bất đồng về guideline**, không phải về bức ảnh. Sửa guideline trước, sửa nhãn sau.
