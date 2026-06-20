# Appendix

This supplementary material provides additional details for the paper, including prompt templates, parsing rules, post-processing settings, and cross-domain diagnostic configurations.

A PDF version of the appendix is also provided in `docs/appendix.pdf`.

## A. Prompt and Parsing Details

This appendix summarizes the prompts and parsing rules used for the direct VLM localization baselines. All direct VLM baselines are evaluated under a unified parsing and post-processing protocol. These experiments are designed to diagnose whether large vision-language models can directly perform dense building landmark localization in UAV aerial images without task-specific fine-tuning.

### A.1 Strict Bbox Prompt for Qwen3-VL

In the strict bbox setting, the model is required to directly output building bounding boxes in JSON format. A representative prompt is:

```text
Identify all buildings in this aerial image. Return only a valid JSON object with the key "boxes". Each box must be in the format [x1, y1, x2, y2] in pixel coordinates, where the origin is the top-left corner of the image. The boxes should tightly cover individual building roofs. Do not include roads, vegetation, shadows, background regions, or partial inaccurate boxes. If there is no building, return {"boxes": []}. Do not output explanations, markdown, or any text outside the JSON.
```

The original strict prompt often fails to enumerate dense building instances and may return malformed or incomplete JSON outputs. Therefore, we further evaluate an enhanced strict prompt, denoted as prompt-v2.

### A.2 Enhanced Strict Bbox Prompt-v2

The enhanced prompt-v2 reinforces three types of constraints: output format, dense instance enumeration, and bounding-box tightness. A representative version is:

```text
Detect every visible building roof in the aerial image. Output only a valid JSON object:
{
  "boxes": [
    [x1, y1, x2, y2],
    ...
  ]
}
Coordinates must be pixel coordinates in the image, with x increasing to the right and y increasing downward. Each box must tightly cover one building roof. Do not merge multiple buildings into one box. Do not output roads, trees, cars, shadows, background, or incomplete building fragments. If no building is visible, output { "boxes": [] }. The answer must be valid JSON only, without markdown or explanations.
```

Compared with the original strict prompt, this prompt improves the parsing success rate and coarse localization performance of Qwen3-VL. However, its COCO-style AP remains much lower than proposal-based and supervised detection methods.

### A.3 Flexible Point/Box Prompt

In the flexible point/box setting, the model is allowed to output either building center points or bounding boxes. This setting is used to test whether a VLM can at least identify approximate building centers when precise bounding-box generation is difficult. A representative prompt is:

```text
Find all buildings in this aerial image. For each building, output either its center point or its bounding box. Return only a valid JSON object with the key "objects". Each object may contain:
{
  "point": [x, y],
  "bbox": [x1, y1, x2, y2]
}
Coordinates must be pixel coordinates. If only a center point is available, provide the point. If a tight box is available, provide the bbox. Do not output non-building regions. Do not include any text outside the JSON.
```

For flexible outputs, the primary metric is Point-AP. Pseudo COCO AP is reported only as a diagnostic value after converting point predictions into pseudo boxes. It should not be interpreted as standard bbox AP.

### A.4 Molmo English Prompt-v2

For Molmo, we use an English flexible prompt because it more reliably follows English localization instructions in our experiments. A representative prompt is:

```text
Look at the aerial image and locate all building roofs. Return a JSON list of building locations. Each building should be represented by either a center point [x, y] or a bounding box [x1, y1, x2, y2] in image pixel coordinates. Output JSON only. Do not include explanations. Do not mark roads, vegetation, shadows, background, or non-building objects.
```

Molmo flexible English prompt-v2 improves Point-AP compared with the original flexible prompt, but its point-level recognition performance remains much weaker than proposal-based methods.

### A.5 JSON Repair and Parsing Rules

Because direct VLM outputs are often not strictly valid JSON, we apply a unified JSON repair and parsing protocol before evaluation.

First, markdown fences, code block markers, leading explanations, and trailing comments are removed. If the model outputs multiple text fragments, the parser extracts the first bracket-matched JSON object or JSON array. Common key names are normalized, including:

```text
boxes
bbox
bboxes
detections
objects
points
centers
```

Numeric strings are converted to floating-point values whenever possible. Predictions with missing coordinates, non-numeric values, or invalid lengths are discarded. For bbox outputs, both `[x1, y1, x2, y2]` and `[x, y, w, h]` formats are handled when the format can be inferred. Coordinates are clipped to image boundaries. Boxes with non-positive width or height are discarded. When a VLM does not provide confidence scores, predictions are assigned monotonically decreasing scores according to their output order.

### A.6 Coordinate Normalization Rules

The parser supports both absolute pixel coordinates and normalized coordinates. If all coordinate values lie in `[0, 1]`, they are interpreted as normalized coordinates and multiplied by the image width or height. If coordinates appear to be percentages, they are converted to pixel coordinates accordingly.

All final coordinates are clipped to the valid image range. For point outputs, a prediction is considered valid if the point lies inside the image. Point predictions are evaluated using the unified point-level recognition protocol. For pseudo bbox diagnostics, pure point predictions are converted into small pseudo boxes by the evaluation script. These pseudo AP values only indicate coarse localization quality and should not be interpreted as standard bbox AP.

### A.7 Duplicate Removal and NMS Rules

For direct bbox outputs, non-maximum suppression is applied before evaluation to remove duplicate and highly overlapping predictions. For proposal-based methods, duplicate suppression follows the locked post-processing configuration described in the main paper.

Specifically, RV-VLM-LA-BR uses score fusion and Gaussian Soft-NMS. The locked inference parameters are:

```text
score fusion alpha = 0.4
Soft-NMS IoU threshold = 0.1
Soft-NMS sigma = 2.0
maximum detections per image = 100
```

This appendix only describes the parsing-side duplicate removal used for direct VLM outputs.

## B. Post-processing and Cross-domain Diagnostic Details

This appendix provides additional post-processing verification and cross-domain diagnostic details that complement the main paper.

### B.1 Soft-NMS Post-processing Validation

To validate the locked post-processing pipeline, we compare two Gaussian Soft-NMS sigma values on the WHU first-50 subset using the same raw candidate scores. The final evaluation uses score fusion alpha of 0.4, Soft-NMS IoU threshold of 0.1, maximum detections per image of 100, and Soft-NMS sigma of 2.0.

| Post-processing | AP50:95 | AP50 | AP75 | AR@100 |
|---|---:|---:|---:|---:|
| Gaussian Soft-NMS, sigma=2.0 | 0.5524 | 0.7541 | 0.6310 | 0.7021 |
| Gaussian Soft-NMS, sigma=20.0 | 0.5474 | 0.7351 | 0.6326 | 0.7040 |
| Locked first50 reference | 0.5523 | 0.7558 | 0.6220 | 0.7024 |

The sigma=2.0 setting is closest to the locked first-50 reference in AP50:95, AP50, and AR@100. Therefore, Gaussian Soft-NMS with sigma=2.0 is used in the final post-processing configuration.

### B.2 Inria BoxRefine Diagnostic Discussion

The complete Inria cross-dataset diagnostic results are reported in the main paper. The key diagnostic variant is RV-VLM-LA-BR without BoxRefine at inference time. This variant uses the same WHU-trained region verification scores as RV-VLM-LA-BR but keeps the original UPN proposal boxes instead of applying the WHU-trained BoxRefinementHead.

This diagnostic variant performs better than the refined-box version on Inria. The result suggests that the WHU-trained region verifier transfers better across datasets than the WHU-trained box refinement module. In other words, proposal re-ranking and semantic region verification provide the main cross-dataset gains, while geometric box refinement is more sensitive to domain shift.

This observation motivates future work on multi-domain or domain-adaptive box refinement for aerial building localization.

### B.3 Locked Inference Hyperparameters

The following table summarizes the locked inference configuration used for the final WHU and Inria evaluations. These settings are selected on the WHU validation split and are not tuned on Inria. We do not tune alpha, refine lambda, or Soft-NMS parameters on the Inria evaluation set. This keeps the Inria experiment as a cross-dataset diagnostic rather than a test-time hyperparameter search.

| Hyperparameter | Value |
|---|---|
| Max UPN proposals per image | 128 |
| Score fusion exponent alpha | 0.4 |
| Inference refine lambda | 0.75 |
| Soft-NMS type | Gaussian |
| Soft-NMS IoU threshold | 0.1 |
| Soft-NMS sigma | 2.0 |
| Max detections per image | 100 |
| Min box size | 2 pixels |
| Inria hyperparameter tuning | No |

## C. Repository Usage

This repository is intended to provide supplementary appendix material for reproducibility and transparency. It includes prompt templates, parsing rules, post-processing settings, and diagnostic details.

This repository does not include training data, private annotations, full model checkpoints, or the full paper manuscript.

If this material is used, please cite the corresponding paper.
