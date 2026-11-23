# UAV-PLA revision supplementary documentation

Manuscript: **TAES-2026-2714**  
Title: **UAV-PLA: A Proposal-Conditioned Landmark-Localization Architecture for UAV Vision-Language Navigation in Urban Scenarios**

- `splits/`: six traceable WHU split manifests (train4096, val512, valid-735, test2017 total, positive/zero-annotation test subsets).
- `results/`: manuscript Tables VII–X as summary CSV; **not** original per-image prediction files.
- `configs/`: sanitized v32 and v41 evaluation configuration extracts. These are not full E2/E16 reproduction bundles.
- `docs/whu_split_audit_summary.md`: source-independent quantitative summary of the full SHA256 audit.

WHU, Inria, and DIOR raw images are third-party datasets and are **not redistributed** here. DIOR experiments use training-derived category-specific internal cohorts, not the official DIOR test set. E15 timing reports computer-side component-stage costs, not onboard UAV latency. The old valid-735 subset contains val512 and is not an independent evaluation cohort.
