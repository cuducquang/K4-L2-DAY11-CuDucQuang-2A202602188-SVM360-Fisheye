# Guideline patch

- **Rule mới đề xuất:** gồm ba đoạn bổ sung cho R06, R07 và R02. Chỉ đề xuất trong file này; `docs/02-rules-vi.md` giữ nguyên.
  1. **R06-a: ngưỡng `unreadable`.** Vật cao ≥H (40 px) chỉ được phủ `ignore_region` reason `unreadable` khi có đủ cả hai điều kiện sau:
     - hai người soát độc lập không xác định được đó có phải một trong năm class hay không (người/xe);
     - ghi chú kèm polygon nêu lý do cụ thể: mờ chuyển động, cháy sáng hoặc tối hoàn toàn.
     Nếu thấy rõ là phương tiện nhưng chưa chắc class (ví dụ Truck hay ThreeWheeler chở hàng), vẫn phải vẽ box với class có khả năng cao nhất và ghi finding `E5_unresolved`. Không được dùng `unreadable` thay cho câu hỏi về class.
  2. **R07-a: hình dạng `ego_body`.** Polygon `ego_body` phải bám sát phần nhìn thấy của xe ego và của người đang điều khiển hoặc ngồi trên xe ego (tay, chân, thân). Không dùng hình chữ nhật trùm lên vùng có người hay xe khác. Một vật của bên thứ ba (người đứng, xe đỗ) nằm sát ego thì vẫn phải có box riêng theo R01. Kiểm tra bắt buộc: không có box reference nào nằm ≥50% trong polygon `ego_body` (hệ quả trực tiếp của R09).
  3. **R02-a: mảnh nhìn thấy bị vật che chia cắt.** Khi vật che (chân người, cột) chia một phương tiện thành nhiều mảnh nhìn thấy, box bao mọi mảnh chắc chắn thuộc vật đó và đặt `occluded=true`. Không cắt box chỉ còn mảnh lớn nhất. Nếu mảnh nhỏ không chắc thuộc vật nào, bỏ mảnh đó và ghi E5.
- **Áp dụng cho:**
  - `ignore_region` với reason `unreadable` và `ego_body`, trên mọi class.
  - Zone `edge`/`mid` sát vành kính, nơi người lái ego và các vật cận cảnh bị kéo giãn.
  - Box `occluded` của Bike/ThreeWheeler trong cảnh đông.
- **Vì sao luật hiện tại (`docs/02-rules-vi.md`) không đủ:**
  - R01 bắt box vật ≥H, trong khi R06 cho phép `unreadable` nhưng không có ngưỡng. Hệ quả ở `adasind_145860.jpg`: reference phủ `unreadable` lên chiếc xe vàng cao 58 px (L2) mà người gán và model M4 đều nhận ra là Truck. Ảnh: `screenshots/unreadable_145860.png`.
  - R07 chỉ liệt kê "thân xe/gương/tay lái", không nói về người lái của xe ego hay hình dạng polygon. Hệ quả:
    - Reference dùng hình chữ nhật ego_body nuốt một ThreeWheeler thật (R4), chân một người đứng (`adasind_199770.jpg`) và một người đi bộ (`adasind_167700.jpg` L10).
    - Người lái áo caro bên trái lại không được che, nên model M4 bắt người lái thành Pedestrian.
    - Ảnh: `screenshots/ref_ego_rect_199770.png` và `screenshots/ref_ego_rect_167700.png`.
  - R02 nói "phần nhìn thấy" nhưng không nói về các mảnh bị chia cắt. Hệ quả: ở 167700 L7, tôi lấy cả mảnh bánh trước giữa hai chân người, còn R và M đều bắt đầu từ x≈427–430.
- **`rules_version` mới:** v1.0.0 → **v1.1.0**. Đây là bổ sung làm rõ, không đổi class hay attribute, nên tăng minor.
- **Hiệu lực từ:**
  - Áp dụng từ vòng `r1_craft` của lô tiếp theo.
  - Với B3-dense: reference cần được sửa theo R07-a trước (`30_escalation_ticket.md`, Ticket 1), rồi chạy lại `local-quality` và `rework` để so sánh.
  - Các findings đã ghi trong lô này giữ `rules_version=v1.0.0` để truy vết được.
