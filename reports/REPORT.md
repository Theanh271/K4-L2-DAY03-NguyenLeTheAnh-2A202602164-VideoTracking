# Báo cáo Ngày 3 — Tracking Annotation

Họ tên / nhóm: Nguyễn Lê Thế Anh
Ngày: 15/9/2026


## 1. Quá trình gán nhãn

| Mục | Giá trị |
| --- | --- |
| Công cụ | CVAT |
| Thời gian gán `clip_02` (warm-up) | `15` phút |
| Thời gian gán `clip_01` | `85` phút |
| Số track đã vẽ trong `clip_01` | `16 ` |
| Số keyframe trung bình mỗi track | `80.25` |

Ba tình huống khó nhất khi gán clip này, và bạn xử lý thế nào:

1.Chưa biết khi xe đi khỏi ảnh thì sẽ xử lý thế nào với rectangle track, đã hỏi LabCoach để hiểu rõ hơn về cách thức.
2.Khó xác định khi xe bị che khuất, đã kiểm tra lại ID và giữ nguyên track của xe.
3.Khó chỉnh box khi xe thay đổi hướng hoặc kích thước, đã thêm keyframe để box bám sát xe hơn.

## 2. Tự kiểm và kiểm chéo

Ba lượt tua bắt được gì (lượt 1 nhìn ID, lượt 2 frame đầu/cuối, lượt 3 frame giữa):

Lượt 1: Kiểm tra ID, phát hiện các trường hợp ID bị đổi hoặc nhầm giữa các xe.
Lượt 2: Kiểm tra frame đầu/cuối để xem xe xuất hiện hoặc rời khỏi ảnh có được xử lý đúng không.
Lượt 3: Kiểm tra frame giữa để xem bbox có bị lệch hoặc trôi khỏi xe không.

Kiểm chéo với: `Solo`. Chi tiết ở `reports/review_partner.md`.



## 3. Pre-gold lock và chấm trước/sau rework

### 3.1. Kết quả trước rework

Kết quả so sánh nhãn của tôi với gold:

* IDF1: **0.940** → Đạt
* MOTA: **0.873** → Đạt
* MOTP: **0.856** → Đạt
* ID switch: **0**
* FP: **71**
* FN: **2**

Kết quả đạt cả 3 ngưỡng của bài. Tuy nhiên vẫn còn một số bbox thừa và bbox hơi lệch.

### 3.2. Kết quả model trước và sau khi dùng ReID

**ByteTrack control:**

* HOTA: **0.709**
* IDF1: **0.875**
* MOTA: **0.749**
* MOTP: **0.823**
* ID switch: **2**

ByteTrack chưa đạt MOTA vì **0.749 < 0.75**.

**BoT-SORT + ReID:**

* HOTA: **0.763**
* IDF1: **0.900**
* MOTA: **0.792**
* MOTP: **0.860**
* ID switch: **2**

BoT-SORT + ReID đạt cả 3 ngưỡng. So với ByteTrack, các chỉ số HOTA, DetA, AssA, LocA, IDF1, MOTA và MOTP đều tăng. Tuy nhiên số ID switch vẫn là , nên ReID chưa loại bỏ hoàn toàn lỗi đổi ID.

### 3.3. Các lỗi cần chú ý

* ID switch ở frame **87**: track gold 5 đổi từ ID 17 sang ID 18.
* ID switch ở frame **113**: track gold 6 đổi từ ID 24 sang ID 31.
* Một số bbox bị lệch, ví dụ frame **104, 109, 113, 114**.
* Có bbox thừa ở một số ID như **7, 27, 38**.
* Track gold 6 chỉ được model phủ **44/56 frame (79%)**.

### 3.4. Nhận xét

Kết quả **BoT-SORT + ReID tốt hơn ByteTrack** trên các metric chính. ReID giúp cải thiện khả năng phát hiện, giữ ID và vị trí bbox. Tuy nhiên vẫn còn ID switch và bbox thừa nên cần tiếp tục kiểm tra các frame bị lỗi.



## 4. Kết quả model: ByteTrack control vs ReID treatment

Cấu hình từ `outputs/model_run_config.json`:

| Mục                                | Giá trị                                                                             |
| ---------------------------------- | ----------------------------------------------------------------------------------- |
| Python / ultralytics / torch / lap | `Python 3.13.15 / ultralytics 8.4.145 / torch 2.11.0+cpu / lap 0.5.13`              |
| weights / hai tracker              | `yolo26n.pt / ByteTrack: bytetrack.yaml / ReID: configs/trackers/botsort-reid.yaml` |
| conf / IoU / imgsz / classes       | `0.25 / 0.7 / 960 / [2, 5, 7]`                                                      |
| device                             | `cpu`                                                                               |

| So sánh                   |  HOTA |  DetA |  AssA |  LocA |  IDF1 |  MOTA |  MOTP | FP | FN | IDSW |
| ------------------------- | ----: | ----: | ----: | ----: | ----: | ----: | ----: | -: | -: | ---: |
| bạn vs gold               | 0.773 | 0.755 | 0.792 | 0.869 | 0.940 | 0.873 | 0.856 | 71 |  2 |    0 |
| ByteTrack control vs gold | 0.709 | 0.649 | 0.776 | 0.846 | 0.875 | 0.749 | 0.823 | 88 | 54 |    2 |
| BoT-SORT + ReID vs gold   | 0.763 | 0.711 | 0.820 | 0.872 | 0.900 | 0.792 | 0.860 | 91 | 26 |    2 |
| ReID vs bạn               | 0.718 | 0.659 | 0.785 | 0.876 | 0.859 | 0.721 | 0.865 | 87 | 91 |    1 |

### Nhận xét

BoT-SORT + ReID có kết quả tốt hơn ByteTrack ở các chỉ số HOTA, DetA, AssA, LocA, IDF1, MOTA và MOTP. MOTA tăng từ `0.749` lên `0.792`, IDF1 tăng từ `0.875` lên `0.900`.

Tuy nhiên, số ID switch của hai model đều là `2`, nên ReID chưa loại bỏ hoàn toàn lỗi đổi ID.


## 5. Phân tích — năm câu hỏi

**1.** MOTA `0.873` thấp hơn IDF1 `0.940`. MOTA không phạt lỗi ID mạnh bằng IDF1.

**2.** ByteTrack: IDF1 `0.875`, AssA `0.776`, IDSW `2`. ReID: IDF1 `0.900`, AssA `0.820`, IDSW `2`. ReID tốt hơn ở IDF1 và AssA, nhưng IDSW không đổi. Ví dụ frame `87`, ReID vẫn đổi ID `17 → 18`. Không thể kết luận riêng do ReID vì hai tracker khác nhau.

**3.** DetA tăng `0.649 → 0.711`, FN giảm `54 → 26`, nhưng FP tăng `88 → 91`. Vẫn còn cả lỗi bbox/detection và association.

**4.** Frame `87`, track `5`: nhãn của tôi giữ ID, ReID đổi `17 → 18`, nên model bị ID switch.

**5.** Frame `113`, track `6`: ReID đổi `24 → 31`. Tôi kiểm tra lại annotation và thấy không cần đổi ID, nên evidence nghiêng về model sai.

## 6. Nếu phải gán thêm 10 clip nữa

Em sẽ ghi guideline rõ hơn ở mấy chỗ lúc làm bị vướng, nhất là xe bị che, xe ra khỏi khung và lúc bbox bị trôi.

Lúc gán em sẽ để ý thêm các frame ở giữa keyframe, không chỉ nhìn đầu với cuối. Làm xong cũng sẽ kiểm tra lại ID và `Outside` để tránh bị thừa hoặc đổi ID.


## 7. Tệp đã nộp

- [x] `annotations/clip_01/gt.txt`
- [x] `annotations/clip_02/gt.txt`
- [x] `evidence/pre-gold/clip_01/gt.txt` và `manifest.json`
- [x] `GUIDELINE_MINI.md` 
- [x] `outputs/eval_vs_gold.json`
- [x] `outputs/model_bytetrack_clip_01.txt`
- [x] `outputs/model_reid_clip_01.txt`
- [x] `outputs/model_run_config.json`
- [x] `outputs/eval_bytetrack_vs_gold.json`, `outputs/eval_reid_vs_gold.json`, `outputs/eval_reid_vs_me.json`
- [x] `reports/review_partner.md`
- [x] `reports/REPORT.md` 
