# Machine-readable paper summary tables

These four CSV files are **transcriptions of published summary values** from TAES-2026-2714 revised manuscript, Section V.H-J, Tables VII-X.
They are NOT per-image prediction files, do not independently reconstruct COCO AP,
and should not be described as raw experimental data.

- `whu_test2017_table_vii.csv`: four verifiers on 1,729 annotation-positive and all 2,416 registered images.
- `zero_annotation_table_viii.csv`: potential detections at score >= 0.50 on 687 zero-annotation images.
- `dior_table_ix.csv`: DIOR E14 Bridge / Storage tank category-specific internal confirmation results, with 1,000 paired bootstrap resamples.
- `runtime_e15_table_x.csv`: DIOR E15 synchronized component-stage totals and separate cold-start batch times; not validated onboard latency.

**Evaluation caution:** the 1,729-image paired bootstrap intervals are not valid confidence intervals for the full 2,416-image AP values.
Original WHU, Inria and DIOR images are not redistributed.
