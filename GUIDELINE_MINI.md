# Mini annotation guideline — Ngày 3 (tracking)


Nhóm / tên: Nguyễn Lê Thế AnhAnh

Clip: `clip_01`, `clip_02`


## 1. Phạm vi: gán cái gì, không gán cái gì

Một lớp duy nhất: **`vehicle`** — xe bốn bánh (xe con, van, xe buýt, xe tải).

| Gán                           | Không gán                                           |
| ----------------------------- | --------------------------------------------------- |
| xe con, SUV, taxi, xe bán tải | người đi bộ                                         |
| van, minivan                  | xe đạp                                              |
| xe buýt, minibus              | xe máy / mô tô                                      |
| xe tải, xe đầu kéo            | xe trong ảnh quảng cáo, trong gương, dưới bóng nước |

Bổ sung của nhóm: Không có

## 2. Luật ID — phần quan trọng nhất

| Tình huống                       | Luật của nhóm                                                                     | Vì sao                                                                       |
| -------------------------------- | --------------------------------------------------------------------------------- | ---------------------------------------------------------------------------- |
| Xe bị che một phần rồi hiện lại  | giữ nguyên ID nếu bị che dưới 25 frame                                            | Giúp giữ đúng một ID cho cùng một xe khi xe chỉ bị che trong thời gian ngắn. |
| Xe bị che lâu hơn ngưỡng trên    | tạo track mới                                                                     | Khi bị che quá lâu khó xác định chắc chắn đó vẫn là cùng xe.                 |
| Xe rời khung hình rồi quay lại   | track mới                                                                         | Xe đã rời khỏi ảnh nên khi quay lại mặc định xem là một track mới.           |
| Hai xe cắt nhau / chồng lên nhau | kiểm tra ID trước và sau khi cắt nhau, không đổi ID nếu vẫn xác định được cùng xe | Tránh ID switch khi hai xe đi gần hoặc che nhau.                             |

## 3. Luật bbox

| Tình huống                             | Luật của nhóm                                                                                                                  |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| Xe bị cắt bởi rìa ảnh                  | bbox chạm đúng rìa, không đoán phần ngoài ảnh                                                                                  |
| Xe bị xe khác che một phần             | bbox ôm phần nhìn thấy được                                                                                                    |
| Xe vừa xuất hiện, còn rất nhỏ / rất mờ | bắt đầu track từ frame đầu tiên xác định được là xe bốn bánh                                                                   |
| Xe đang đỗ, không di chuyển            | vẫn giữ track nếu xe còn xuất hiện trong ảnh                                                                                   |
| Keyframe đặt dày ở đâu                 | đặt thêm keyframe khi xe đổi hướng, thay đổi kích thước hoặc bị che; đặc biệt kiểm tra vùng giữa hai keyframe nếu bbox bị trôi |

## 4. Ít nhất ba ca mơ hồ đã gặp thật

### Ca 1

* Clip / frame / ID: `clip_01 / frame 87 / track 5`
* Tình huống: model ReID đổi ID của cùng một xe từ ID 17 sang ID 18.
* Quyết định: giữ nguyên ID của nhãn.
* Lý do: đây là lỗi ID switch của model, trong khi nhãn của tôi không có ID switch.

### Ca 2

* Clip / frame / ID: `clip_01 / frame 113 / track 6`
* Tình huống: model ReID đổi ID từ 24 sang 31.
* Quyết định: không đổi ID trong annotation.
* Lý do: model bị lỗi association, không phải bằng chứng cho thấy annotation sai.

### Ca 3

* Clip / frame / ID: `clip_01 / frame 81–82 / track 5`
* Tình huống: bbox model bị lệch, IoU lần lượt khoảng 0.51 và 0.57.
* Quyết định: kiểm tra lại vị trí bbox và thêm keyframe quanh đoạn này.
* Lý do: bbox có dấu hiệu bị trôi giữa các keyframe.

## 5. Sửa gì sau khi chấm với gold và sau khi kiểm chéo

* Cần kiểm tra kỹ các đoạn giữa hai keyframe vì bbox có thể bị trôi dù hai đầu keyframe đúng.
* Khi xe rời khỏi khung hình phải đặt `Outside` đúng thời điểm, tránh bbox treo thêm vài frame.
