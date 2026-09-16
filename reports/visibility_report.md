# Visibility report

- Thư mục nhãn: `dataset\labels\train`
- 20 ảnh, 28 skeleton, trung bình 15.89 khớp có v > 0 mỗi người
- Tổng: v=2 318 | v=1 127 | v=0 31

| # | Khớp | v=2 | v=1 | v=0 | %v=1 |
| ---: | --- | ---: | ---: | ---: | ---: |
| 0 | nose | 21 | 7 | 0 | 25% |
| 1 | left_eye | 18 | 10 | 0 | 36% |
| 2 | right_eye | 20 | 8 | 0 | 29% |
| 3 | left_ear | 9 | 19 | 0 | 68% |
| 4 | right_ear | 14 | 14 | 0 | 50% |
| 5 | left_shoulder | 27 | 1 | 0 | 4% |
| 6 | right_shoulder | 26 | 2 | 0 | 7% |
| 7 | left_elbow | 20 | 8 | 0 | 29% |
| 8 | right_elbow | 25 | 3 | 0 | 11% |
| 9 | left_wrist | 19 | 9 | 0 | 32% |
| 10 | right_wrist | 19 | 8 | 1 | 29% |
| 11 | left_hip | 17 | 10 | 1 | 36% |
| 12 | right_hip | 21 | 6 | 1 | 21% |
| 13 | left_knee | 16 | 7 | 5 | 25% |
| 14 | right_knee | 18 | 5 | 5 | 18% |
| 15 | left_ankle | 14 | 5 | 9 | 18% |
| 16 | right_ankle | 14 | 5 | 9 | 18% |

## Đọc bảng này thế nào

1. Khớp nào có **%v=1 cao**: khớp hay bị che. Cổ tay và hông thường là hai vị trí cần xem lại guideline trước khi kết luận.
2. Khớp nào có **v=0 cao bất thường**: mọi người đang dùng Outside ở chỗ đáng lẽ là Occluded. Đó là lỗi số 3 của slide 46, và nó xoá thẳng khớp đó khỏi bảng điểm OKS.
3. Khi so hai người: **lệch lớn = bất đồng về guideline**, không phải về bức ảnh. Sửa guideline trước, sửa nhãn sau.
