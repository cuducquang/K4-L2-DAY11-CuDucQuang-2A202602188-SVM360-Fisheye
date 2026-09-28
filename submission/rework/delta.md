# Rework delta

| zone | matched before | matched after | missing before | missing after | spurious before | spurious after |
|---|---:|---:|---:|---:|---:|---:|
| center | 8 | 9 | 1 | 0 | 1 | 1 |
| mid | 5 | 6 | 2 | 1 | 3 | 1 |
| edge | 2 | 4 | 2 | 0 | 1 | 0 |

## Findings action=rework
- adasind_019560.jpg L3 SPURIOUS: không áp dụng
- adasind_019560.jpg L4+R5 BOX_GEOMETRY: không áp dụng
- adasind_167700.jpg L1+R7 WRONG_CLASS: đã sửa
- adasind_199770.jpg L3+R5 BOX_GEOMETRY: đã sửa
- adasind_199770.jpg L4+R7 WRONG_CLASS: đã sửa
- adasind_199770.jpg L5 SPURIOUS: đã sửa
- adasind_199770.jpg R4 MISSING: chưa sửa
- adasind_199770.jpg R6 MISSING: đã sửa
- adasind_167700.jpg L1 SPURIOUS: đã sửa
- adasind_167700.jpg R7 MISSING: đã sửa
- adasind_199770.jpg L3+M5 SPURIOUS: đã sửa
- adasind_199770.jpg L4+M6 SPURIOUS: đã sửa
- adasind_199770.jpg L5 SPURIOUS: đã sửa
- adasind_199770.jpg R4 MISSING: chưa sửa
- adasind_199770.jpg R5 MISSING: đã sửa
- adasind_199770.jpg R6 MISSING: đã sửa
- adasind_199770.jpg R7 MISSING: đã sửa
- adasind_199770.jpg M13 SPURIOUS: không áp dụng

## Đọc số (ghi tay, bảng trên do lệnh tạo)

- Tổng trước → sau: matched 15 → 19, missing 5 → 1, spurious 5 → 2. Mỗi thay đổi gắn với một quyết định trong `40_decision_log.csv`:
  - D2: 199770 Bike → ThreeWheeler + Pedestrian.
  - D3: tách hai Bike, Car → Truck, xóa xe đẩy. Edge matched 2 → 4, mid spurious 3 → 1.
  - D4: 167700 Car → Truck. Center/mid missing về 0/1.
- `199770 R4 MISSING: chưa sửa` là lỗi phạm vi của reference, không phải nhãn còn thiếu. v2 L1 ThreeWheeler (925,818)-(1080,1305) có IoU≈0.60 với R4, nhưng nằm ≥50% trong hình chữ nhật `ego_body` của reference nên bị loại (R09). Ca này được escalate ở `30_escalation_ticket.md` (Ticket 1). Tôi không sửa nhãn đúng chỉ để khớp số.
- Spurious còn lại:
  - 167700 L3: chiếc auto thứ hai, reference không có box riêng.
  - 199770 L12: người trong hiên tối, thêm theo r3_diag M13.
  - Cả hai là `E0_reference_defect`, được giữ có lý do.
- `adasind_019560.jpg` (C0) và `M13` ghi "không áp dụng" vì không thuộc slice B3-dense hoặc không có chỉ số R/L để truy.
