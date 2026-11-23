# WHU Dataset Split Documentation

This directory documents the dataset partitions used in the UAV-PLA experiments.

- train4096: 4,096 proposal-verification training records.
- val512: 512 images used for checkpoint selection and hyperparameter calibration.
- valid-735: 735 images used for historical development-stage evaluation.
- test2017: 2,416 images used for independent testing.

All 512 val512 images are included in valid-735. Therefore, valid-735 is treated as a development-stage evaluation set rather than an independent test set.

The separate WHU test2017 partition contains:
- 1,729 images with 48,591 annotated building instances.
- 687 images with no annotated building instances in the fixed evaluation annotations.

The split manifests identify the actual records and image IDs used in the reported experiments.

The original WHU images are not redistributed in this repository.
