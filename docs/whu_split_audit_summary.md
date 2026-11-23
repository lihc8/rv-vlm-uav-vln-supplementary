# WHU split audit summary

**Audit type:** complete original-image file-byte SHA256 comparison and manifest reconciliation (local read-only audit).

- Actual proposal-verification training records: **4,096**, matching the E2 planned 4,096-entry filename set.
- Checkpoint-selection subset: **512** images; all included in the historical **735**-image development set.
- Full registered test2017 split: **2,416** images.
- Test2017 fixed annotations: **48,591** instances across **1,729** annotation-positive images; **687** registered images have no building annotations.
- Training/test basenames in common: **580**; identical image **SHA256 contents: 0** after reading all **4,096** train and **2,416** test image files.

**Interpretation:** These checks support byte-level disjointness of the evaluated image files. They do not rule out visually similar images, re-encodings, overlapping crops of the same source photograph, or other dataset provenance issues. The registered test2017 partition was used for separate frozen evaluation, not historical valid-735 checkpoint selection. No test image memberships were altered during this audit.

**What is publicly shared:** Image-ID/filename manifests and summarized audit counts, not the original WHU image files, private local machine paths, or raw per-image predictions. Numeric image IDs may be partition-local.
