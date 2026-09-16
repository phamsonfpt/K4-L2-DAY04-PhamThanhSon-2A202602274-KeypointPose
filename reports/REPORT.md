# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: Phạm Thanh Sơn   Nhóm: 2A   Ngày: 16/09/2026

## 1. Nhãn của tôi

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 28 |
| v=2 / v=1 / v=0 | 318 / 127 / 31 |
| Thời gian trung bình mỗi ảnh | ~4 phút |

Ba khớp có `%v=1` cao nhất (chép từ `reports/visibility_report.md`):

1. `left_ear` — 68% (19/28 skeleton)
2. `right_ear` — 50% (14/28 skeleton)
3. `left_eye` — 36% (10/28 skeleton)

Chúng có đúng là những khớp bạn thấy khó gán nhất không? Nếu không, giải thích.

Đúng. Tai là khớp bị che nhiều nhất vì phần lớn ảnh chụp người từ góc chính diện hoặc 3/4,
khiến tai phía sau đầu gần như luôn bị che bởi tóc hoặc đầu. Mắt trái cũng thường bị che
khi người quay mặt sang phải. Đây là những khớp khó gán không phải vì vị trí giải phẫu
khó xác định mà vì chúng thật sự không quan sát được trong ảnh.

## 2. Chấm với gold

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | 0.910 | |
| OKS@0.50 | 0.931 | |
| OKS@0.75 | 0.931 | |
| Lỗi `dao_trai_phai` | 1 | |
| Lỗi `nham_nguoi` | 0 | |
| Lỗi `xoa_khop_bi_che` | 0 | |

**Tôi đã sửa gì giữa hai lần chạy** (ghi cụ thể: ảnh nào, người thứ mấy, khớp nào):

- `train_13.jpg` người #2: Thêm annotation bị bỏ sót hoàn toàn (OKS = 0.000 → thiếu người)
- `train_09.jpg` người #1: Sửa `left_shoulder`, `right_shoulder`, `left_hip`, `right_hip` bị đảo ngược trái/phải; chỉnh `left_ankle` bị trượt hẳn ra khỏi vị trí khớp thực tế

**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào?** Ảnh đó dễ hay khó? Nếu là ảnh dễ,
bạn nghĩ vì sao mình vẫn sai?

Lỗi đảo trái/phải xảy ra ở `train_09.jpg`. Ảnh có người đứng quay lưng về phía camera
nên vai và hông không hiển thị rõ ràng — đây là ảnh khó. Tôi đã nhầm chiều do không dùng
hướng mặt nhìn làm mốc để xác định trái/phải cơ thể.

## 3. Kiểm chéo

Bạn cùng nhóm: _(chưa thực hiện kiểm chéo)_

Khớp lệch `%v=1` nhiều nhất giữa hai bảng đếm:

| Khớp | Bạn | Họ | Lệch | Nguyên nhân (guideline hay gán sai?) |
| --- | ---: | ---: | ---: | --- |
| left_ear | 68% | | | |
| right_ear | 50% | | | |

Luật mới đã bổ sung vào `GUIDELINE_MINI.md` sau khi thống nhất:

- Khi tai bị che một phần bởi tóc nhưng vành tai còn ước lượng được vị trí trong khung ảnh
  → dùng `v=1` (occluded), không dùng `v=0` (outside). Chỉ dùng `v=0` khi tai hoàn toàn
  ra ngoài khung hoặc bị vật cứng che hoàn toàn không thể đặt chấm ước lượng.

## 4. Model

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | 0.8450 | 0.8450 | +0.0000 |
| pose_mAP50-95 | 0.6853 | 0.6908 | +0.0055 |
| pose_precision | 0.9734 | 0.9792 | +0.0058 |
| pose_recall | 0.8462 | 0.8462 | +0.0000 |
| box_mAP50-95 | 0.8119 | 0.8041 | -0.0078 |

### Trả lời năm câu hỏi ở cuối notebook

> Mỗi câu cần trỏ tới ảnh/chỉ số cụ thể.

1. **`pose_mAP50-95` thay đổi bao nhiêu?**

   `pose_mAP50-95` tăng +0.0055 (từ 0.6853 lên 0.6908). Mức tăng rất nhỏ vì 20 ảnh
   quá ít so với tập COCO (~118k ảnh) mà model đã được pre-train. Fine-tune trên tập nhỏ
   giúp model quen thêm với phong cách ảnh cụ thể nhưng không đủ để cải thiện đáng kể
   độ chính xác tổng quát. `box_mAP50-95` lại giảm nhẹ (-0.0078), có thể do model
   "quên" một phần khả năng tổng quát khi overfit vào 20 ảnh.

2. **`box_mAP` và `pose_mAP` chênh nhau bao nhiêu?**

   Sau fine-tune: `box_mAP50-95` = 0.8041, `pose_mAP50-95` = 0.6908, chênh 0.1133.
   Model tìm *người* (bounding box) dễ hơn tìm *khớp* vì phát hiện hình dạng tổng thể
   của người đơn giản hơn xác định chính xác vị trí từng điểm trong 17 keypoint —
   đặc biệt với các khớp bị che hoặc ngoài khung.

3. **Một ảnh test model đoán sai — gọi tên lỗi theo slide 43:**

   _(Cần xem ảnh từ cell 14 trên Colab để xác định cụ thể.)_
   Dựa trên cảnh báo của `check_pose_labels.py`, các ảnh có người quay lưng
   (`train_09`) là nơi model dễ mắc lỗi **"đảo trái/phải"** — tương tự lỗi tôi
   đã mắc khi annotate.

4. **Ảnh OKS thấp nhất giữa nhãn của bạn và model:**

   _(Cần số từ cell 16 trên Colab.)_
   Theo kết quả gold, `train_09.jpg` là ảnh tôi gán tệ nhất (OKS gold = 0.214).
   Nếu model cũng cho OKS thấp ở ảnh này, tôi sẽ dựa vào ảnh gold để xác định
   ai đúng — gold là đáp án chuẩn.

5. **Ảnh gán tệ nhất có trùng model đoán tệ nhất không?**

   `train_09.jpg` là ảnh tôi gán tệ nhất (lỗi đảo trái/phải + trượt khớp).
   Nếu model cũng đoán tệ ở đây, điều đó cho thấy người quay lưng hoàn toàn
   là trường hợp *khó khách quan* — cả người annotate lẫn model đều thiếu
   thông tin thị giác để xác định trái/phải cơ thể.

## 5. Một rule evidence tôi đã dùng

Trong `train_15.jpg`, người thứ 1 có `left_ear` (tai trái) bị tóc che một phần.
Tôi quan sát thấy vành tai còn nhô ra khoảng 30–40%, vị trí tai vẫn ước lượng được
dựa trên vị trí mắt và xương hàm liền kề. Vì tai vẫn còn trong khung ảnh và có thể
đặt chấm ước lượng hợp lý, tôi chọn `v=1` (occluded) thay vì `v=0` (outside).
Nếu dùng `v=0`, khớp này bị loại khỏi tính OKS mặc dù vị trí của nó vẫn có thể
đóng góp thông tin cho model khi train.
