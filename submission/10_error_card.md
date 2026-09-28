# Error analysis card

## Zone × block

| zone | block | what | count |
|---|---|---|---:|
| center | B3 | BOX_GEOMETRY | 1 |
| center | B3 | MISSING | 2 |
| center | B3 | SPURIOUS | 6 |
| center | B3 | WRONG_CLASS | 1 |
| center | C0 | BOX_GEOMETRY | 1 |
| center | C0 | SPURIOUS | 1 |
| edge | B3 | BOX_GEOMETRY | 1 |
| edge | B3 | IGNORE_SCOPE | 1 |
| edge | B3 | MISSING | 5 |
| edge | B3 | SPURIOUS | 3 |
| mid | B3 | BOX_GEOMETRY | 1 |
| mid | B3 | IGNORE_SCOPE | 8 |
| mid | B3 | MISSING | 5 |
| mid | B3 | SPURIOUS | 11 |
| mid | B3 | WRONG_CLASS | 2 |
| mid | C0 | SPURIOUS | 1 |
| unknown | B3 | IGNORE_SCOPE | 1 |

## Top defects
- SPURIOUS: 22 (ví dụ frame adasind_019560.jpg)
- MISSING: 12 (ví dụ frame adasind_199770.jpg)
- IGNORE_SCOPE: 10 (ví dụ frame adasind_145860.jpg)

## Phân tích của bạn

Hai bảng trên do `python3 lab11.py card` tính từ `findings.csv`; chạy lại lệnh sẽ cập nhật bảng và giữ nguyên mục này. Viết cho lỗi nổi bật nhất, dẫn frame/`object_ref`.

- Nguyên nhân khả dĩ (`why`) và vì sao bạn nghĩ vậy:
  - **SPURIOUS là lỗi nhiều nhất, nhưng không phải chủ yếu do tôi vẽ thừa.** Trong 22 dòng SPURIOUS có ba nhóm:
    - (a) Lỗi class/tách-gộp của tôi (`E1_annotator_error`): khi một vật bị gán sai class hoặc bị gộp, bảng đếm ghi cả SPURIOUS lẫn MISSING. Các ca: 167700 L1 Car thay vì Truck; 199770 L3 gộp hai Bike, L4 Car thay vì Truck, L5 xe đẩy gọi ThreeWheeler.
    - (b) Vật thật mà reference không có (`E0_reference_defect`): 167700 L3 là chiếc auto thứ hai; 199770 v2 L12 là người trong hiên tối.
    - (c) Model thừa (`E4_model_domain`): ThreeWheeler bị gọi Car/Truck (167700 M8; 199770 M8, M12) và box một phần (167700 M5, 199770 M11).
  - Tôi kết luận E1 cho nhóm (a) vì ảnh phóng to 3–6x cho thấy rõ thùng hàng, hai xe tách nhau và cột xe đẩy không có bánh. Lúc gán, tôi đã chọn class theo hình khối thay vì theo chi tiết.
  - **IGNORE_SCOPE (10 dòng) đến từ reference.** Hai hình chữ nhật `ego_body` nuốt vật thật (E0, P0), và một vùng `unreadable` phủ lên vật ≥H (E2).
- Cách sửa và ai nhận việc (`owner`):
  - `annotator` (tôi): đã rework 5 ca và thêm 1 người. Theo `rework/delta.md`, matched tăng 15→19, missing giảm 5→1, spurious giảm 5→2. Missing còn lại là R4, chỉ do ignore của reference.
  - Thói quen mới: phóng to mọi cụm xe ≥4x và tăng sáng vùng tối trước khi chọn class.
  - `qa`: sửa hai ego_body của reference (Ticket 1).
  - `guideline`: R06-a, R07-a, R02-a trong `20_guideline_patch.md` (v1.1.0).
  - `ai_team`: thêm dữ liệu ThreeWheeler/auto và ca người lái ego vào tập huấn luyện/đánh giá. Model gọi ThreeWheeler thành Car/Truck ở 4 ca và bắt người lái ego thành Pedestrian ở 2 frame (145860 M3, 199770 M4).
- Bằng chứng (ảnh trong `screenshots/`, dòng findings, rule):
  - Ảnh:
    - `screenshots/rework_199770_left.png`: trước/sau cụm xe trái, R01/R04.
    - `screenshots/rework_167700_truck.png`: Car→Truck, R04.
    - `screenshots/ref_ego_rect_199770.png` và `ref_ego_rect_167700.png`: ego_body của reference, R07/R09.
    - `screenshots/unreadable_145860.png`: R06.
    - `screenshots/dark_shop_199770.png`: người bị sót trong hiên tối, R01.
  - Dòng findings: `r1_craft` 167700 L1+R7, 199770 L3+R5 / L4+R7 / L5 / R4; `r3_diag` 199770 L3+M5, L4+M6, M13 (rework) và M9 (E5, cần xem frame liền kề); các dòng `rework` có action=escalate.
  - Số đo: `r3_diag/local_quality.md` (P/R 0.750, confusion: 2 Truck của reference bị gán Car) và `rework/delta.md`.
