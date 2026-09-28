# Kế hoạch review từ lỗi quan sát được

Từ `findings.csv` và `zone_table.md`, chọn **hai lát cắt của bài ADASIND một camera** cần review trước. Bảng này
giải thích dữ liệu thật bạn vừa làm; nó không thay cho kế hoạch bốn camera giả lập ở `45_sampling_plan.csv`.

| Lát cắt / frame | Số ca và loại lỗi | Vì sao review trước | Bằng chứng cần giữ |
|---|---|---|---|
| **Vành kính mép trái/phải, cạnh ego_body** (199770 và 167700, zone edge và mid sát vành) | 10 dòng IGNORE_SCOPE trong findings (phần lớn P0 do ego_body của reference). Theo zone_table, M sót 4/4 vật reference ở edge. 199770 R4 vẫn MISSING sau rework vì bị ignore nuốt. | Lỗi P0 về phạm vi làm sai mọi số ở zone edge: vật thật bị loại khỏi phép so, còn người lái ego lại bị tính là vật thừa. Chừng nào chưa sửa, mọi kết luận về edge đều không tin được. | `screenshots/ref_ego_rect_199770.png`, `screenshots/ref_ego_rect_167700.png`; `r3_diag/model_compare.md`; `rework/delta.md`; Ticket 1. |
| **Cụm xe ba bánh và xe đỗ trong phố đông** (199770 x≈0–330, 167700 x≈0–600) | 4 ca E4 model gọi ThreeWheeler thành Truck/Car (167700 M8; 199770 M7, M8, M12). 4 ca E1 của tôi: gộp hai Bike, Car→Truck ×2, xe đẩy bị gọi ThreeWheeler. Theo zone_table, L thừa nhiều nhất ở mid (3). | Đây là class đặc thù của dữ liệu (auto/xe ba bánh) mà cả người lẫn model đều nhầm. Lỗi lặp lại qua nhiều frame nên là mẫu, không phải ca lẻ. | `screenshots/rework_199770_left.png`, `screenshots/rework_167700_truck.png`; `r3_diag/local_quality_confusion.csv` (2 Truck của reference bị gán Car); các dòng findings E4 và E1. |

Giới hạn của kết luận từ ba frame ADASIND:
- Chỉ có 3 frame với 20 vật reference, cùng một chuyến đi, cùng ánh sáng ban ngày và một camera. Mỗi zone chỉ có 4–9 vật, nên một vật đổi trạng thái làm tỷ lệ lệch 11–25 điểm.
- Reference có lỗi đã biết (Ticket 1), nên precision/recall 0.750 trong `local_quality.md` chỉ là tín hiệu để tìm ca cần soát, không phải chất lượng nhãn.
- Các mẫu "model yếu với ThreeWheeler" và "edge gãy nhiều" là giả thuyết. Cần thêm frame từ nhiều cảnh độc lập để kiểm chứng.

## Chuyển sang kế hoạch bốn camera giả lập

Cách soát độ phủ của 200 frame ở `45_sampling_plan.csv` và giới hạn của kế hoạch:
- **Độ phủ:** mỗi camera có cả normal (85 frame tổng) và hard (115 frame). Hard dồn nhiều hơn vào left/right (30 mỗi camera), vì camera bên có seam với front/rear và vật sát xe, đúng kiểu lỗi edge và ego_body đã thấy.
- **Không đếm trùng:**
  - Đơn vị đếm là một *cảnh/sự kiện*, không phải một frame.
  - Trong một clip liên tục, lấy tối đa 1 frame mỗi 5 giây. Một lần vượt xe, một lần lùi đỗ hay một lần đi qua seam được tính là một ca, dù xuất hiện trên nhiều frame hoặc hai camera.
  - Rải mẫu qua nhiều chuyến, giờ và thời tiết. Có một bảng kiểm "chuyến × camera × điều kiện" để thấy ô nào chưa có mẫu.
  - Với ca seam, frame của hai camera cùng timestamp được ghép thành một ca.
- **Giới hạn:**
  - Hard slice được chọn có chủ đích theo rủi ro (dense, edge, seam, tối), nên tỷ lệ lỗi đo trên 200 frame này cao hơn tỷ lệ thật của toàn bộ 50.000 frame.
  - Kế hoạch chỉ giúp tìm và phân loại ca cần soi. Muốn ước lượng tỷ lệ lỗi, cần thêm một mẫu ngẫu nhiên phân tầng có trọng số và reference đã được kiểm chứng.
