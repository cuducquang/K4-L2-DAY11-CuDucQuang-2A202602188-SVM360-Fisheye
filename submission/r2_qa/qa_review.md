# QA review · B3-dense

Mã khóa: 1093-F4B9

Hình thức: cold review bản đã khóa của chính mình, chỉ dựa trên ảnh gốc, `qa_overlay.html` và `docs/02-rules-vi.md` (rules v1.0.0). Chưa mở reference, model overlay hay worked HTML của B3-dense. Theo `team.json`, người soát chéo chính thức bài này là nguyentrongthang. Nhận xét của bạn ấy sẽ được dẫn vào decision log khi có.

| frame | object_ref | rule_id | nhận xét |
|---|---|---|---|
| adasind_199770.jpg | L9 | R01 | Box `Pedestrian` (852,858)-(900,940) nằm trong hiên quán rất tối. Khi tăng sáng, có một mảng đỏ ở (870–895, 865–880) và thân tối bên dưới. Có thể là người ngồi, cũng có thể là quần áo/hàng treo. Không tự khẳng định được đây là người: cần người thứ hai xem ảnh gốc. Nếu không phải người thì box này thừa. |
| adasind_199770.jpg | L10 | R01 | Box `Pedestrian` (925,860)-(958,942) cũng ở hiên tối. Thấy một mảng trắng giống khẩu trang/mũ ở (930–950, 865–880), còn thân không rõ. Cùng rủi ro thừa box như L9. |
| adasind_199770.jpg | L1 | R03 | Xe tay ga đen sát mép phải, có người lái. Box gộp xe và người lái thành một `Bike`, kéo xuống tới bàn chân mang dép ở y≈1560. Cần kiểm tra lại hai điều: chân/dép đó có thuộc người lái xe này không, hay là chân người ngồi sau trên xe ego (nếu thuộc ego thì đó là phạm vi R07). Nếu chân không thuộc xe này thì đáy box phải lên khoảng y≈1310, tức đáy bánh trước. |
| adasind_199770.jpg | L7+L8 | R03 | Người áo trắng đứng cạnh xe tay ga đỏ, tay chạm ghi đông, không ngồi lên. Tôi tách `Pedestrian` + `Bike`. Nếu người này thực ra đang ngồi hoặc dắt xe thì quyết định vẫn đúng. Chỉ khi người đó đang lái thì mới phải gộp thành một Bike. Giữ nguyên, ghi lại để đối chiếu. |
| adasind_145860.jpg | L2 | R04 | Xe màu vàng ở xa, bên trái đường, chỉ cao 58 px và rất mờ. Tôi gán `Truck` vì thấy thùng vuông phía sau. Tuy vậy, ở độ phân giải này không loại được máy kéo (máy kéo vẫn là `Truck` theo R04) hay ThreeWheeler chở hàng. Class có rủi ro. |
| adasind_167700.jpg | L8 | R04 | Khối xám hình hộp (552,873)-(595,938) ở cuối phố. Tôi gán `Truck` (thùng xe tải nhìn từ sau). Có khả năng đây là ki-ốt/quầy hàng chứ không phải xe, khi đó box này thừa. |
| adasind_167700.jpg | L7 | R02 | Xe đạp chở thùng của người áo sọc: box (365,950)-(600,1195) lấy cả bánh sau bị chân người che một phần. Bánh sau chỉ thấy lờ mờ nên mép trái x≈365 có thể rộng quá. Cần soát xem box có bám đúng phần nhìn thấy không. |
| adasind_145860.jpg | ego_body #2 | R07 | Polygon ego nhỏ ở (250–345, 1735–1806) bao một bàn chân mang dép sát vành kính, nhiều khả năng là chân người ngồi trên xe ego. R07 chỉ nói "thân xe/gương/tay lái". Việc coi chân/tay người lái của xe mang camera là `ego_body` là suy diễn của tôi và cần được xác nhận (liên quan cả polygon người lái áo caro ở cả 3 frame). |
| adasind_199770.jpg | (không box) | R01 | Đã soát 2 mảng trắng có thể là người: (530–550, 850–890) cạnh ThreeWheeler L6 và (970–990, 858–885) trong hiên. Phóng to thấy đó là thùng/bao hàng, không phải người → không thiếu box. |

Ghi finding r2_qa: cell=L_only, rule_id có giá trị, why để trống.
