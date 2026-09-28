# Đối chiếu chất lượng cục bộ — rectangle

Teaching reference, không phải gold set đã phê duyệt; không có điểm đạt tự động.
Nguồn: export r1_craft đã khóa SHA256 `1093f4b9f91765da06b198644c3c86ec345d110126afea208ffe238b38bb815e`; slice `B3-dense`.
Ghép hình học greedy một-một theo IoU ≥ 0.50, rồi so class; H ≥ 40 px.
Box trái nằm chủ yếu trong ignore_region reference không tính. Polygon, polyline, track không được chấm.
Đây là phép tính offline của lab, không phải báo cáo hay kết quả tương đương CVAT Premium.

Frame được tính: adasind_145860.jpg, adasind_167700.jpg, adasind_199770.jpg. Frame thiếu trong export: không.
TP=15; FP=5; FN=5; số lần đối chiếu=23; mean IoU của TP=0.748.

| Chỉ số | Micro | Macro | Nhãn thấp nhất |
|---|---:|---:|---:|
| accuracy | 0.652 | 0.913 | 0.870 |
| precision | 0.750 | 0.780 | 0.500 |
| recall | 0.750 | 0.783 | 0.500 |
| jaccard | 0.600 | 0.614 | 0.500 |
| dice | 0.750 | 0.745 | 0.667 |

| Nhãn | TP | FP | FN | Accuracy | Precision | Recall | Jaccard | Dice |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| Bike | 4 | 1 | 2 | 0.870 | 0.800 | 0.667 | 0.571 | 0.727 |
| Car | 2 | 2 | 0 | 0.913 | 0.500 | 1.000 | 0.500 | 0.667 |
| Pedestrian | 4 | 0 | 0 | 1.000 | 1.000 | 1.000 | 1.000 | 1.000 |
| ThreeWheeler | 3 | 2 | 1 | 0.870 | 0.600 | 0.750 | 0.500 | 0.667 |
| Truck | 2 | 0 | 2 | 0.913 | 1.000 | 0.500 | 0.500 | 0.667 |

| Frame | TP | FP | FN | Accuracy | Precision | Recall |
|---|---:|---:|---:|---:|---:|---:|
| adasind_145860.jpg | 2 | 0 | 0 | 1.000 | 1.000 | 1.000 |
| adasind_167700.jpg | 8 | 2 | 1 | 0.800 | 0.800 | 0.889 |
| adasind_199770.jpg | 5 | 3 | 4 | 0.455 | 0.625 | 0.556 |

Confusion matrix: hàng = teaching reference; cột = export đã khóa.
`<missing>` là thiếu box; `<extra>` là box thừa. Xem `local_quality_confusion.csv`.

| Reference \ Export | Bike | Car | Pedestrian | ThreeWheeler | Truck | <missing> |
|---|---:|---:|---:|---:|---:|---:|
| Bike | 4 | 0 | 0 | 0 | 0 | 2 |
| Car | 0 | 2 | 0 | 0 | 0 | 0 |
| Pedestrian | 0 | 0 | 4 | 0 | 0 | 0 |
| ThreeWheeler | 0 | 0 | 0 | 3 | 0 | 1 |
| Truck | 0 | 2 | 0 | 0 | 2 | 0 |
| <extra> | 1 | 0 | 0 | 2 | 0 | 0 |

Chi tiết xung đột trong `local_quality_conflicts.csv`; dữ liệu máy đọc trong `local_quality.json`.
Mismatching label đóng góp một FP cho class vẽ và một FN cho class reference; attribute khác được báo riêng.
Micro accuracy đếm mỗi cặp ghép sai class là một lần đối chiếu; Jaccard đếm cả FP và FN.
Macro/worst bỏ nhãn không xuất hiện ở cả hai phía; chỉ số không có mẫu là N/A.
