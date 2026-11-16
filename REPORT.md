# Medium (M) YOLO Segmentation Benchmark — CCTV600

## 1. Experiment Status

COMPLETE for accuracy and dedicated clean timing. Each of 3 models has 600 accepted unique image packets. Evaluation includes 769 eligible person boxes. Dataset-quality and interpretation limitations remain documented below.

## 2. Models Tested

| Family | Checkpoint | Parameters | GFLOPs estimate | Weights MB |
| --- | --- | --- | --- | --- |
| YOLO26 | yolo26m-seg.pt | 27,112,072 | 132.781 | 54.750 |
| YOLO11 | yolo11m-seg.pt | 22,420,896 | 113.968 | 45.400 |
| YOLOv8 | yolov8m-seg.pt | 27,285,968 | 104.977 | 54.921 |

## 3. Protocol Compatibility

| Item | Status and evidence |
| --- | --- |
| Dataset | PASS · 600 exact IDs; dataset membership SHA in frozen config |
| Evaluator | PASS · GT-first bbox matching and shared ignore union |
| Preprocessing | PASS · frozen runtime source binding |
| Input size | 1×3×640×640, centered letterbox |
| Precision | FP32; no AMP/TF32/extra TTA |
| Thresholds | AP floor .001, fixed conf .25, match IoU .50, NMS IoU .70 |
| maxDet | Model 1000 / evaluator 200; cap parity PASS |
| Timing | 3×100 clean observations, warmup 10 each round; sample order verified |
| Environment | Python 3.12.3 / torch 2.14.0+cu130 / Ultralytics 8.4.160 / Tesla T4 |

Compatibility applies within this CCTV600 experiment. MOTS20 supplies the reporting structure and reused detector runtime. CCTV600 uses visible box GT, its ≥50 px eligibility and coarse rectangle-ignore policy. MOTS20 uses person silhouettes and mask matching.

## 4. Overall Results

| Model | Box AP % | AP75 % | Recall % | Forward ms/ภาพ | Adapter ms/ภาพ | FPS | Allocated MiB |
| --- | --- | --- | --- | --- | --- | --- | --- |
| yolo26m-seg.pt | 61.437 | 69.662 | 74.252 | 29.719 | 56.126 | 17.817 | 605.934 |
| yolo11m-seg.pt | 60.436 | 67.273 | 73.212 | 30.418 | 61.257 | 16.325 | 696.762 |
| yolov8m-seg.pt | 61.489 | 69.790 | 75.423 | 30.927 | 69.127 | 14.466 | 766.317 |

| Model | Precision % | Recall % | F1 % | TP | FP | FN | Ignored | TP-only box IoU |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| yolo26m-seg.pt | 85.224 | 74.252 | 79.361 | 571 | 99 | 198 | 19 | 0.875618 |
| yolo11m-seg.pt | 87.832 | 73.212 | 79.858 | 563 | 78 | 206 | 23 | 0.869691 |
| yolov8m-seg.pt | 84.919 | 75.423 | 79.890 | 580 | 103 | 189 | 20 | 0.869575 |

AP/P/R/F1 columns use percentages. TP-only box IoU is a fraction. Counts use confidence ≥0.25 and box IoU ≥0.50. AP uses 10 IoU thresholds, 101 recall points and maxDet 200. Source-precision values remain in CSV.

## 5. Tier Winners

| ด้าน | โมเดล | ค่าที่วัด |
| --- | --- | --- |
| Box AP | yolov8m-seg.pt | 61.489 % |
| AP75 | yolov8m-seg.pt | 69.790 % |
| Recall | yolov8m-seg.pt | 75.423 % |
| Forward time | yolo26m-seg.pt | 29.719 ms/ภาพ |
| Adapter time | yolo26m-seg.pt | 56.126 ms/ภาพ |
| Adapter FPS | yolo26m-seg.pt | 17.817 ภาพ/วินาที |
| Allocated VRAM | yolo26m-seg.pt | 605.934 MiB |

## 6. Key Findings

The highest Box AP is yolov8m-seg.pt (61.489%), 0.052 percentage points above the runner-up. The shortest adapter mean belongs to yolo26m-seg.pt (56.126 ms/image). Differences are descriptive; no significance or architecture-causality claim is made.

## 7. Per-group Observations

| Model | Day AP % | Night AP % | R 50–99 px % | R 100–199 px % | R ≥200 px % |
| --- | --- | --- | --- | --- | --- |
| yolo26m-seg.pt | 53.936 | 67.426 | 48.876 | 78.655 | 86.345 |
| yolo11m-seg.pt | 51.992 | 67.059 | 42.135 | 78.655 | 87.952 |
| yolov8m-seg.pt | 54.731 | 66.820 | 46.067 | 78.070 | 92.771 |

Day/night use existing metadata and contain different scenes and people. Pooled AP is read directly from the canonical full-population result. Size-band recall conditions on GT size and the original matching; precision/AP by size were not computed under a new policy.

## 8. Efficiency and Resource Observations

| Model | Pre mean ms | Post mean ms | Adapter P50 ms | Adapter P95 ms | Adapter std ms | Reserved MiB |
| --- | --- | --- | --- | --- | --- | --- |
| yolo26m-seg.pt | 2.134 | 24.274 | 49.502 | 96.780 | 21.145 | 1036.000 |
| yolo11m-seg.pt | 1.897 | 28.943 | 54.130 | 113.996 | 24.967 | 1086.000 |
| yolov8m-seg.pt | 1.924 | 36.275 | 60.741 | 134.142 | 29.670 | 1248.000 |

Means, median, P95 and population standard deviation use all 300 accepted observations. Adapter FPS excludes disk/decode, RLE, packet writing and load/warmup. Native postprocessing substage timers are inclusive subsets. Allocated/reserved peaks include the resident model and take the maximum across the three accepted rounds.

## 9. Warnings and Anomalies

The original study ended with incomplete Nano timing. A linked segment supplied four accepted clean rounds, retaining the original contaminated YOLO11n round and original completion unchanged. Accuracy was not rerun. Detailed accepted round paths and hashes are in the common result index. The owned web worker was restored after authorized temporary suspension.

## 10. Limitations

GT inventory relies on the dataset creator's 600-image acceptance. Independent all-person inventory adjudication remains unavailable. No full-person silhouette GT, mask AP, tracking or parsing/color score is reported here. TP-only IoU excludes missed people. Different pretrained capacities and training histories confound architecture attribution. Related frames and absent uncertainty analysis limit generalization.

## 11. Reproducibility and Source Artifacts

[Canonical tier table](metrics/TIER_RESULTS.csv), [timing statistics](metrics/TIMING_SUMMARY.csv), [shared source/protocol/checkpoint bindings](https://github.com/folklazy/YOLO_Instance_Segmentation_CCTV600_Scaling_Study/blob/main/metrics/RESULT_INDEX.json), [closed-run checks](https://github.com/folklazy/YOLO_Instance_Segmentation_CCTV600_Scaling_Study/blob/main/metrics/CLOSEOUT.json), [metric definitions](https://github.com/folklazy/YOLO_Instance_Segmentation_CCTV600_Scaling_Study/blob/main/METRIC_GUIDE_TH.md) and [presentation revision evidence](https://github.com/folklazy/YOLO_Instance_Segmentation_CCTV600_Scaling_Study/blob/main/metrics/PRESENTATION_REVISION.json).

## 12. Relation to Full Scaling Study

[Largest (X/E)](https://github.com/folklazy/YOLO_Large_Seg_CCTV600_Benchmark) · [Second-largest (L/C)](https://github.com/folklazy/YOLO_Second_Largest_Seg_CCTV600_Benchmark) · [Medium (M)](https://github.com/folklazy/YOLO_Medium_Seg_CCTV600_Benchmark) · [Small (S)](https://github.com/folklazy/YOLO_Small_Seg_CCTV600_Benchmark) · [Nano (N)](https://github.com/folklazy/YOLO_Nano_Seg_CCTV600_Benchmark) · [Master](https://github.com/folklazy/YOLO_Instance_Segmentation_CCTV600_Scaling_Study)

## Qualitative Analysis

[PRESENTATION_SUMMARY_TH.md](PRESENTATION_SUMMARY_TH.md) discusses four diagnostic cases from the original fixed16. Full frames, identical ROIs and per-GT matching are retained in the private local gallery. Only aggregate charts and de-identified qualitative observations are published.
