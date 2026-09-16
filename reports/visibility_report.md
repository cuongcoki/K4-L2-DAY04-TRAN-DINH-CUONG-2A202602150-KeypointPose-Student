# Visibility report

- Thư mục nhãn: `dataset\labels\train`
- 20 ảnh, 27 skeleton, trung bình 16.11 khớp có v > 0 mỗi người
- Tổng: v=2 336 | v=1 99 | v=0 24

| # | Khớp | v=2 | v=1 | v=0 | %v=1 |
| ---: | --- | ---: | ---: | ---: | ---: |
| 0 | nose | 21 | 6 | 0 | 22% |
| 1 | left_eye | 18 | 9 | 0 | 33% |
| 2 | right_eye | 19 | 8 | 0 | 30% |
| 3 | left_ear | 14 | 13 | 0 | 48% |
| 4 | right_ear | 15 | 12 | 0 | 44% |
| 5 | left_shoulder | 27 | 0 | 0 | 0% |
| 6 | right_shoulder | 25 | 2 | 0 | 7% |
| 7 | left_elbow | 23 | 4 | 0 | 15% |
| 8 | right_elbow | 22 | 5 | 0 | 19% |
| 9 | left_wrist | 18 | 9 | 0 | 33% |
| 10 | right_wrist | 20 | 6 | 1 | 22% |
| 11 | left_hip | 21 | 6 | 0 | 22% |
| 12 | right_hip | 22 | 5 | 0 | 19% |
| 13 | left_knee | 21 | 2 | 4 | 7% |
| 14 | right_knee | 20 | 4 | 3 | 15% |
| 15 | left_ankle | 16 | 3 | 8 | 11% |
| 16 | right_ankle | 14 | 5 | 8 | 19% |

## Đọc bảng này thế nào

1. Khớp nào có **%v=1 cao**: khớp hay bị che. Cổ tay và hông thường là hai vị trí cần xem lại guideline trước khi kết luận.
2. Khớp nào có **v=0 cao bất thường**: mọi người đang dùng Outside ở chỗ đáng lẽ là Occluded. Đó là lỗi số 3 của slide 46, và nó xoá thẳng khớp đó khỏi bảng điểm OKS.
3. Khi so hai người: **lệch lớn = bất đồng về guideline**, không phải về bức ảnh. Sửa guideline trước, sửa nhãn sau.
