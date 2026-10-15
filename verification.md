| # | Check | App shows | pandas gives | Match? |
|---|---|---|---|---|
| 1 | r for `age` vs `systolic_bp`, all participants | 0.436  |0.46269506081575096 | yes|
| 2 | r for `bmi` vs `waist_cm`, all participants |0.903 |0.9025887758609342 |yes |
| 3 | r for `waist_cm` vs `hdl_chol`, all participants |-0.335 |-0.33498687631212654 |yes |
| 4 | r and n for `age` vs `systolic_bp`, **Female only** |n=0.287, r=750 |0.2866974002939496 750 |yes |
| 5 | r and n for `age` vs `systolic_bp`, **Male only** |r=0.159, n=673 |0.15925602856961588 673 |yes |
| 6 | With the filter set to Female, does the heatmap's `age`/`systolic_bp` square match row 4? |0.29,  n=750|matches row 4 |yes |