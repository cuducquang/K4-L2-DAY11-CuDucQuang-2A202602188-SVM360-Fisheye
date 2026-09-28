# Exit ticket

Đọc `docs/10-svm360-reading-vi.md` trước khi trả lời câu 1–2. Các câu về zone, `why`, rework, parking và sampling
đã nằm trong file tương ứng nên không hỏi lại ở đây.

1. Một vật ở vùng seam giữa hai camera thật xuất hiện với hai box khác nhau: đó là lỗi `DUPLICATE` hay cần một quy
   tắc riêng? Vì sao?

   **Trả lời:** Cần một quy tắc riêng, không tính là `DUPLICATE`.
   - `DUPLICATE` là hai box cho cùng một vật trên **cùng một ảnh**.
   - Ở seam, mỗi camera là một ảnh độc lập. Theo luật gán trên ảnh gốc, mỗi ảnh đều phải có box cho vật nhìn thấy, nên hai box ở hai camera là hợp lệ, dù hình dạng và zone có thể khác nhau (edge ở camera này, mid ở camera kia).
   - Quy tắc riêng cần nói rõ ba điều:
     - gán theo từng camera;
     - gắn cờ hoặc liên kết `seam` khi có timestamp đồng bộ và calibration chứng minh hai box là cùng một vật;
     - việc hợp nhất thành một vật là của tầng fusion theo policy output đích, không phải annotator tự xóa một box.

2. Một vật đi qua nhiều frame trên cùng camera: khi nào giữ cùng track ID, khi nào thêm keyframe hoặc trạng thái
   Outside? Nêu bằng chứng sẽ cần trước khi nối track qua hai camera.

   **Trả lời:**
   - **Giữ cùng track ID** khi vẫn là cùng một vật và còn quan sát được liên tục, kể cả khi bị che một phần trong vài frame (đặt `occluded`).
   - **Thêm keyframe** khi hình học đổi nhiều: box đổi kích thước hoặc vị trí vượt mức nội suy chấp nhận được, nhất là khi vật chạy từ center ra edge và bị kéo giãn hoặc cắt, khi class nhìn rõ hơn, hoặc khi trạng thái occluded/truncated thay đổi.
   - **Đặt Outside** khi vật rời khỏi trường nhìn hoặc bị che hoàn toàn. Khi vật quay lại, chỉ tiếp tục ID cũ nếu có bằng chứng đó là cùng vật; nếu không thì tạo ID mới.
   - **Trước khi nối track qua hai camera**, cần:
     - timestamp đồng bộ giữa hai camera;
     - intrinsics/extrinsics để chiếu hai quan sát về cùng hệ tọa độ, và thấy vị trí và vận tốc khớp nhau;
     - class và đặc điểm ngoại hình nhất quán;
     - policy output đích cho biết track cross-camera có được hợp nhất hay không.
   - Thiếu một trong các bằng chứng trên thì giữ hai track riêng và ghi E5.

3. Nhìn lại cả buổi: một chỗ bạn tin nhãn mình đúng nhưng reference hoặc người soát nghĩ khác (dẫn frame/`object_ref`),
   bạn đã xử lý thế nào, và nếu làm lại slice này bạn sẽ đổi gì trong cách làm?

   **Trả lời:**
   - **Chỗ bất đồng:** `adasind_199770.jpg`, vật sát mép phải. Reference đặt hình chữ nhật `ego_body` (924,813)-(1080,1552) trùm lên đó.
   - **Cách xử lý:**
     - Tôi phóng to ảnh gốc và xác định đó là một ThreeWheeler thật cùng chân một người đứng, không phải xe ego.
     - Cái sai của tôi là gọi nó là Bike, và tôi đã rework lại: v2 L1 ThreeWheeler và L2 Pedestrian.
     - Tôi không đổi nhãn để khớp vùng ignore của reference. Tôi ghi `E0_reference_defect` với action=escalate và mở Ticket 1 kèm ảnh `screenshots/ref_ego_rect_199770.png`.
     - Tôi cũng chấp nhận rằng delta vẫn báo R4 "chưa sửa", và giải thích lý do thay vì sửa số.
     - Tương tự ở 167700 L3: tôi giữ chiếc auto thứ hai mà reference không có box riêng.
   - **Nếu làm lại slice này, tôi sẽ:**
     1. Phóng to từng cụm xe dày đặc ngay từ đầu (≥4x), trước khi chọn class. Ba lỗi Car/Truck/ThreeWheeler của tôi đều sửa được nhờ ảnh phóng to.
     2. Tăng sáng các vùng tối ngay ở vòng craft, để không bỏ sót người như ca M13.
     3. Tách từng xe trong cụm xe đỗ thay vì gộp.
     4. Hỏi rõ phạm vi `ego_body` (người lái xe ego có tính không) trước khi gán, thay vì chỉ ghi câu hỏi vào QA.
