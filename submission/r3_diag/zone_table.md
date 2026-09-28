# Zone table (slice của bạn)

Lệnh `python3 lab11.py model` tự ghi bảng số (cùng cách đếm với `r1_craft/compare.md` và `model_compare.md`); chạy lại lệnh sẽ cập nhật bảng và giữ nguyên phần nhận xét. Bạn chỉ viết mục Nhận xét.

| Zone | n_ref | L missing | L spurious | M missing (`LR_noM` + `R_only`) | M thừa (`LM_noR` + `M_only`) | Lỗi L chính (`what`) |
|---|---:|---:|---:|---:|---:|---|
| center | 9 | 1 | 1 | 2 | 3 | WRONG_CLASS (1) |
| mid | 7 | 2 | 3 | 3 | 4 | SPURIOUS (2) |
| edge | 4 | 2 | 1 | 4 | 3 | BOX_GEOMETRY (1) |

## Nhận xét

- Zone nào người (L) và model (M) gãy nhiều nhất, dẫn số ở bảng trên:
  - **Edge là nơi model gãy nặng nhất:** M bỏ sót 4 trên 4 vật reference (100%) và báo thừa 3. Cả 4 vật bị sót đều ở mép trái:
    - 167700 R6: auto vàng, bị model gộp với auto thứ hai thành Truck (M8).
    - 199770 R2: auto bị cắt ở mép, model gán Car (M8).
    - 199770 R5 và R6: hai xe máy đậu, bị gộp vào một box M5.
  - Ở edge, L sót 2 trên 4 (chính là cặp R5/R6 mà tôi gộp làm một) và thừa 1.
  - **Mid là nơi L thừa nhiều nhất:** 3 box thừa, lỗi chính là SPURIOUS. Đó là 167700 L1 (Car thay vì Truck) và L3 (auto thứ hai), cùng 199770 L5 (xe đẩy có mái bạt). Ở mid, M cũng thừa 4 và sót 3.
  - **Center ổn nhất:** trên 9 vật reference, L sót 1 và thừa 1; M sót 2 và thừa 3.
- Giả thuyết vì sao (méo fisheye, box lỏng, thiếu `ego_body`, ...) và giới hạn của slice ba frame:
  - **Edge:** vật bị kéo giãn theo phương bán kính và bị vòng lens cắt. Model được huấn luyện trên ảnh phẳng nên nhận hình dáng méo của ThreeWheeler thành Car/Truck (E4_model_domain). Ngoài ra, các vật đậu sát nhau chỉ rộng 30 px nên dễ bị gộp, cả ở L lẫn M.
  - **Hai box M thừa ở edge là do thiếu `ego_body`, không phải lỗi model thuần túy.** M4 (199770) bắt trúng người lái áo caro của xe ego. Reference không che người này, nên box M4 bị tính là thừa (E0, ticket 30).
  - **Mid:** cảnh phố đông, nhiều quầy hàng và hiên tối. Lỗi ở đây là lỗi đọc class và tách/gộp vật, không liên quan đến méo ảnh.
  - **Giới hạn:** slice chỉ có 3 frame và 20 vật reference, mỗi zone chỉ 4–9 vật. Một vật đổi trạng thái là tỷ lệ của zone lệch 11–25 điểm phần trăm. Hai vùng ignore ego_body hình chữ nhật của reference cũng làm mất 2 vật ở mép phải khỏi phép so. Vì vậy đây chỉ là giả thuyết để chọn mẫu tiếp (xem 45_sampling_plan.csv), chưa phải kết luận thống kê.
