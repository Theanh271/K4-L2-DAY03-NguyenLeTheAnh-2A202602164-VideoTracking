# Peer review — Day 3 (solo)

| Trường           | Giá trị              |
| ---------------- | -------------------- |
| Author           | `Nguyên Leee Thê Anh`                |
| Reviewer         | `Solo - tự kiểm tra` |
| Pair ID          | `N/A - làm solo`     |
| CVAT version     | `2.74.1`             |
| Thời điểm review | `...`                |

## Danh sách finding

|  # | CVAT frame | MOT frame | ID | Loại lỗi | Quan sát + rule áp dụng                                                          | Cách sửa đề xuất            | Closure |
| -: | ---------: | --------: | -: | -------- | -------------------------------------------------------------------------------- | --------------------------- | ------- |
|  1 |         87 |        88 |  5 | ID       | Kiểm tra đoạn xe bị che/cắt nhau, ID phải được giữ nếu vẫn xác định được cùng xe | Giữ nguyên ID của xe        | `fixed` |
|  2 |         81 |        82 |  5 | Bbox     | Bbox ở giữa keyframe bị trôi, cần ôm phần xe nhìn thấy                           | Thêm keyframe và chỉnh bbox | `fixed` |
|  3 |        101 |       102 |  6 | Bbox     | Bbox bị lệch khỏi xe, IoU chỉ khoảng 0.58                                        | Thêm keyframe và chỉnh bbox | `fixed` |

## Reviewer checklist

| Hạng mục                                                   | PASS / FINDING / N/A | Frame–ID–evidence                     |
| ---------------------------------------------------------- | -------------------- | ------------------------------------- |
| Có tối thiểu 6 track hợp lệ; chỉ gồm xe bốn bánh           | PASS                 | Có 8 track trong gold                 |
| Một xe giữ một ID; không reuse ID cho xe khác              | PASS                 | Không có ID switch trong nhãn của tôi |
| Occlusion ngắn giữ ID; crossing không đổi ID               | PASS                 | Kiểm tra lại các đoạn bị che/cắt nhau |
| Entry/exit đúng; không box treo sau khi xe rời khung       | FINDING              | Có kiểm tra lại `Outside`             |
| Bbox ôm phần nhìn thấy, không đoán phần bị che/ngoài khung | PASS                 | Đã kiểm tra bbox                      |
| Frame giữa hai keyframe không bị interpolation drift       | FINDING              | Frame 81–82 và 101                    |
| Export đúng MOT 1.1; frame bắt đầu từ 1; cột 2 là track ID | PASS                 | Export MOT 1.1                        |
| Mọi finding có cách sửa và closure do tác giả điền         | PASS                 | Tự kiểm tra và đóng finding           |

## Self-QC attestation

| Lượt                       | PASS / ĐÃ SỬA / NEEDS-REVIEW | Frame–ID–evidence                  |
| -------------------------- | ---------------------------- | ---------------------------------- |
| 1 — identity/timeline      | ĐÃ SỬA                       | Frame 87, ID 5                     |
| 2 — endpoint/scope         | ĐÃ SỬA                       | Kiểm tra entry/exit và `Outside`   |
| 3 — geometry/interpolation | ĐÃ SỬA                       | Frame 81–82, ID 5; frame 101, ID 6 |

## Exit ticket

1. Finding quan trọng nhất và rule dùng để kết luận: `Bbox bị trôi ở giữa keyframe, cần thêm keyframe để bbox bám sát xe.`

2. Một finding tác giả đóng là `not-a-defect`, kèm lý do: `Chưa có finding nào cần đóng theo cách này.`

3. Một rule cần Lab Coach làm rõ (nếu có): `Không có.`
