# WHU split manifests (UAV-PLA)

These six CSV files were extracted from the original local training/evaluation records and fixed COCO annotation registry. Identifiers refer to their **respective data partitions**; numeric COCO image IDs should **not** be compared directly across independently exported partitions.

- `train4096_record_ids.csv`: 4,096 actual training **records**, reconciled by file name with the 4,096-entry E2 planned index. `source_record_row` is the row number in the input training records, not an original global ID.
- `val512_image_ids.csv`: 512 checkpoint-selection images.
- `valid735_image_ids.csv`: historical 735-image development diagnostics. Every val512 image is included in valid-735.
- `test2017_image_ids.csv`: all 2,416 registered test images.
- `test2017_annotation_positive_ids.csv`: 1,729 images with annotations in the fixed evaluation file.
- `test2017_zero_annotation_ids.csv`: 687 images with no annotations in that file; absence of annotation does not prove absence of real buildings.

The training/test split has overlapping **basenames**; byte-level SHA256 checks on all 4,096 train and 2,416 test image files found no identical image bytes. This does not exclude visually near-duplicate imagery, shared source scenes, or overlapping crops. See `docs/whu_split_audit_summary.md`.

WHU source images are not redistributed here. Obtain originals from the dataset provider under the applicable terms.
