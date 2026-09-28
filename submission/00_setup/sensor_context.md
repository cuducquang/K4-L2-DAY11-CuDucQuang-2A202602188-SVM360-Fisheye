# Sensor context

Slice được giao: **B3-dense** (`adasind_145860.jpg`, `adasind_167700.jpg`, `adasind_199770.jpg`). ADASIND không kèm tài liệu rig chi tiết, nên các ý dưới đây ghi theo quan sát trên ảnh, không phải thông số nhà sản xuất.

- **Rig:**
  - Ảnh dọc 1080×1920 từ một camera fisheye nhìn về phía trước, đặt thấp ngang tầm người lái xe hai bánh.
  - Ở cả ba frame, mép trái luôn có tay, thân và chân của một người mặc áo sơ mi caro. Tay người này đặt trên ghi đông; ở 199770 còn thấy tay nắm ghi đông sát mép trái.
  - Góc dưới có mép xe sơn xanh/đen. Ở 145860, một bàn chân đi dép nằm sát vành kính phía dưới (≈(250–345, 1735–1806)).
  - Tôi hiểu camera được mang trên một xe hai bánh (hoặc cầm/đeo bởi người ngồi sau) và nhìn qua vai phải của người lái. Đây là suy luận từ ảnh, chưa được xác nhận. Vì vậy tôi hỏi lại trong `r2_qa/qa_review.md` (ego_body #2) và trong `30_escalation_ticket.md`.
- **`ego_body` nhìn thấy ở đâu:**
  - Người lái áo caro và phần xe ego ở mép trái, trải từ y≈1000 xuống y≈1720, bề ngang tới x≈135–300 tùy frame.
  - Bàn chân đi dép ở đáy vành kính trong 145860.
  - Tôi phủ các phần này bằng polygon `ignore_region` với reason `ego_body` sát theo hình dạng (R07), không dùng hình chữ nhật.
  - Không có capo hay gương ô tô trong khung.
- **Vòng kính (lens circle):** số liệu lấy từ `assets/frames.csv` của ba frame.
  - Tâm (cx, cy) nằm trong khoảng (553.6–628.2, 983.6–1026.1), bán kính r = 804.9–834.4 px.
  - Đường kính ≈1610–1670 px, lớn hơn bề ngang 1080 px, nên vòng tròn bị cắt ở hai cạnh trái/phải. Theo chiều dọc, vòng kính kéo từ y≈180–220 tới y≈1790–1860.
  - Vòng kính phủ gần như toàn bộ phần giữa khung. Các góc trên và dưới là vùng đen ngoài kính, đã có prefill `lens_border` (R08).
  - Zone `edge` là dải sát vành, gồm cả hai cạnh trái/phải nơi vật bị kéo giãn và cắt. Đây cũng là nơi model gãy nhiều nhất (`r3_diag/zone_table.md`).
