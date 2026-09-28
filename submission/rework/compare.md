# So sánh L với R

Chỉ số L/R là thứ tự box cao ≥ H=40 trong từng frame, theo thứ tự XML; bắt đầu từ 1.
Box L trong ignore_region được báo IGNORE_SCOPE, không tính SPURIOUS.

## adasind_145860.jpg
- L2 mid IGNORE_SCOPE
## adasind_167700.jpg
- L10 mid IGNORE_SCOPE
- L3 mid SPURIOUS
## adasind_199770.jpg
- L1 mid IGNORE_SCOPE
- L2 edge IGNORE_SCOPE
- L11 mid IGNORE_SCOPE
- L12 center SPURIOUS
- R4 mid MISSING

## Theo zone
| zone | n_ref | matched | missing | spurious |
|---|---|---|---|---|
| center | 9 | 9 | 0 | 1 |
| mid | 7 | 6 | 1 | 1 |
| edge | 4 | 4 | 0 | 0 |
