# Đề xuất gold set theo camera — tình huống giả lập

**Đầu bài:** 50.000 frame từ bốn camera SVM, ngân sách chọn 200 frame để review/gold. Đây là tình huống trên slide,
**không phải** 50.000 frame có trong repo. Phân bổ đúng 200 ở `45_sampling_plan.csv` cho bốn camera, mỗi camera có
normal và hard slice. “Gold set” ở đây là **kế hoạch tạo** reference sau kiểm chứng, không phải teaching reference
ADASIND hoặc nhãn bạn vừa vẽ. Nếu cần, dùng `notebooks/day11-svm360-colab.ipynb` để thử tổng phân bổ; notebook
không làm thay phần lý do.

| camera_id | Hard case cần chọn | Vì sao dễ sai | Annotation space / calibration cần giữ | Cách review trước khi gọi là gold |
|---|---|---|---|---|
| front (25 normal / 30 hard) | Phố đông có xe ba bánh, xe tải nhỏ và xe đẩy/quầy hàng; người trong vùng tối hoặc ngược sáng; xe ở xa gần ngưỡng H=40. | Ở ADASIND: ThreeWheeler bị gọi Truck/Car (4 ca); xe tải nhỏ bị gọi Car (2 ca); xe đẩy có bạt bị gọi ThreeWheeler; hai người trong hiên tối bị cả L và R bỏ sót. | Box trên ảnh fisheye gốc (không phải ảnh đã undistort hay BEV). Lưu kèm intrinsics, tâm/bán kính vòng kính của từng camera, phiên bản firmware và `rules_version`. | Hai người gán độc lập, rồi adjudicator thứ ba xử lý mọi cặp IoU<0.5 hoặc lệch class. Ca class mơ hồ ghi E5 và được xem thêm frame video liền kề trước khi chốt. |
| rear (20 / 25) | Xe bám sát hoặc chen, lùi vào ô đỗ, vật thấp sát cản (xe đạp, trẻ em), ban đêm có đèn pha chói. | Vật cận cảnh bị kéo giãn ở edge nên box dễ lỏng; cản sau hoặc biển số của ego dễ bị lẫn với vật; lóa đèn. | Như front, cộng thêm polygon ego_body của cản sau cho từng xe/rig; polygon này đổi khi đổi xe. | Kiểm tự động: không box nào ≥50% trong ego_body. Reviewer soát riêng các box vùng edge, so với frame trước/sau. |
| left (20 / 30) | Vùng seam với front và rear; xe máy vượt sát; người đứng cạnh cửa; vật bị vành kính cắt. | Zone edge là nơi model sót 4/4 ở ADASIND. Camera bên còn thấy gương/thân xe ego, dễ bị ignore quá tay như reference 199770. | Extrinsics giữa các camera (để đối chiếu seam), timestamp đồng bộ, và polygon ego_body theo rig. | Hai người gán độc lập cho mỗi camera. Ca seam được soát cặp front+left cùng timestamp theo policy seam dưới đây. Bất đồng ghi vào decision log, không sửa lặng lẽ. |
| right (20 / 30) | Như left, cộng thêm vật sát mép bị che bởi ego (xe ba bánh, chân người đứng). | Ở ADASIND 199770 mép phải: xe ba bánh bị gán nhầm Bike, và chân một người đứng bị ignore của reference nuốt mất. | Như left. | Như left. Thêm một lượt QA mù dành riêng cho các box chạm ego_body. |

- **Khi nào cần refresh gold set (đổi camera, calibration hoặc rule):**
  - Khi đổi camera, ống kính hoặc vị trí gắn, hoặc khi calibration lệch vượt ngưỡng: tâm vòng kính hay extrinsics đổi, nên zone và seam cũng đổi.
  - Khi đổi xe hoặc rig: ego_body thay đổi.
  - Khi `rules_version` tăng: các frame bị ảnh hưởng (ví dụ v1.1.0 đổi R06-a/R07-a) phải soát lại theo rule mới.
  - Khi model mới lộ ra một kiểu lỗi chưa có trong gold.
  - Định kỳ mỗi quý, thay khoảng 20% mẫu normal để theo phân bố dữ liệu mới.
- **Một ca seam/cross-camera cần policy và evidence trước khi ghép hai box:**
  - Ca: xe máy vượt từ góc trước-trái, cùng lúc có box ở zone edge của camera front và zone mid của camera left.
  - Trước khi ghép thành một vật hoặc nối track, cần bằng chứng:
    - timestamp của hai frame lệch nhau không quá một frame;
    - extrinsics/calibration để chiếu hai box về cùng hệ tọa độ (ví dụ mặt đất/BEV) và thấy chúng chồng lên nhau;
    - policy output đích: giữ cả hai box theo từng camera (per-camera), hay hợp nhất một vật ở tầng fusion.
  - Khi chưa có policy, giữ cả hai box, không ghi DUPLICATE, và gắn cờ seam cho reviewer.
- **Vì sao peer agreement hoặc quality report trên ảnh một camera chưa chứng minh gold set đúng cho cả bốn camera:**
  - Đồng thuận trên một camera chỉ nói hai người nhất quán với nhau. Họ vẫn có thể cùng sai: ở 199770, cả tôi lẫn model đều gọi xe tải là Car, và cả tôi lẫn reference đều bỏ sót một người trong hiên tối.
  - Report ADASIND còn dựa trên một reference có lỗi (Ticket 1).
  - Mỗi camera có ego_body, góc nhìn, tỷ lệ edge và seam riêng. Vì vậy mỗi camera cần review riêng, cộng một lượt soát cross-camera ở seam, trước khi gọi là gold.
