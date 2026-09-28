# So sánh L với R

Chỉ số L/R là thứ tự box cao ≥ H=40 trong từng frame, theo thứ tự XML; bắt đầu từ 1.
Box L trong ignore_region được báo IGNORE_SCOPE, không tính SPURIOUS.

## adasind_145860.jpg
- L2 mid IGNORE_SCOPE
## adasind_167700.jpg
- L10 mid IGNORE_SCOPE
- L1+R7 center WRONG_CLASS
- L3 mid SPURIOUS
## adasind_199770.jpg
- L1 mid IGNORE_SCOPE
- L10 mid IGNORE_SCOPE
- L3+R5 edge BOX_GEOMETRY
- L4+R7 mid WRONG_CLASS
- L5 mid SPURIOUS
- R4 mid MISSING
- R6 edge MISSING

## Theo zone
| zone | n_ref | matched | missing | spurious |
|---|---|---|---|---|
| center | 9 | 8 | 1 | 1 |
| mid | 7 | 5 | 2 | 3 |
| edge | 4 | 2 | 2 | 1 |
