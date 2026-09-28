# Escalation ticket

## Ticket 1: ego_body hình chữ nhật trong teaching reference nuốt vật thật (E0_reference_defect, P0)

- **Frame:**
  - `adasind_199770.jpg`: ignore `ego_body` là hình chữ nhật (924,813)-(1080,1552).
  - `adasind_167700.jpg`: ignore `ego_body` là hình chữ nhật (922,747)-(1077,1443).
- **Ảnh chụp:**
  - `submission/screenshots/ref_ego_rect_199770.png` và `submission/screenshots/ref_ego_rect_167700.png`. Mỗi ảnh đặt L v2 (xanh lá) bên trái và reference (đỏ) bên phải.
  - Vùng đỏ bên phải trùm lên chiếc ThreeWheeler thật (chính reference cũng vẽ R4 bên trong) cùng chân và dép của một người đứng. Người lái áo caro bên trái (ego thật) thì không có polygon.
- **Expected impact:**
  - Trong B3-dense có 4 box thật bị loại khỏi phép so:
    - 199770: v2 L1 ThreeWheeler, L2 Pedestrian, L11;
    - 167700: L10.
  - `rework/delta.md` báo 199770 R4 "chưa sửa", dù ThreeWheeler v2 có IoU≈0.60 với R4. Box bị loại chỉ vì nằm ≥50% trong vùng ignore (R09).
  - Model M4 (người lái ego) bị tính là SPURIOUS, làm số `M thừa` ở zone edge của `zone_table.md` phồng lên.
  - Nếu reference này được dùng làm teaching reference cho các lớp sau, học viên sẽ học sai rằng có thể dùng ego_body để che người/xe cạnh xe ego.
- **Owner:** `qa` (người giữ teaching reference) sửa reference; `guideline` bổ sung R07-a (`20_guideline_patch.md`).
- **Recommendation:**
  1. Thay hai hình chữ nhật bằng polygon `ego_body` bám người lái áo caro và thân xe ego ở mép trái, cùng bàn chân ở đáy vành (như reference đã làm đúng ở 145860).
  2. Giữ R4 ThreeWheeler. Thêm Pedestrian cho người đứng ở mép phải 199770 và cho người trùm khăn ở 167700.
  3. Sau khi sửa, chạy lại `local-quality`/`model`/`rework` và so sánh với số hiện tại.
  4. Thêm một kiểm tra tự động: "không box reference nào ≥50% trong ego_body".
  - Findings liên quan: `r1_craft` 167700 L10, 199770 L1, L10; `r3_diag` 199770 M4; `rework` 199770 L1, L2, L11, R4 (action=escalate).

## Ticket 2: xe ≥H bị đặt `unreadable` (E2_guideline_gap, P2)

- **Frame:** `adasind_145860.jpg`, ignore `unreadable` (238,859)-(281,908) trùm lên L2 Truck (240,852)-(282,910).
- **Ảnh chụp:** `submission/screenshots/unreadable_145860.png`.
- **Expected impact:**
  - Không đổi số của slice này, vì box nằm trong vùng don't-care.
  - Nhưng nếu không có ngưỡng, hai người gán sẽ xử lý khác nhau: người này box, người kia bỏ qua. Bất đồng đó lặp lại ở mọi vật xa và mờ nằm quanh H=40.
  - Model đã thấy vật này là Truck (M4).
- **Owner:** `guideline`.
- **Recommendation:** Áp dụng R06-a trong `20_guideline_patch.md`. Sau đó hai người soát độc lập xem lại vùng này: nếu cả hai xác định được đây là xe thì thay ignore bằng box Truck, và ghi class nào còn nghi vào E5.
