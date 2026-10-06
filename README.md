# Detecting Large Equipment in P&ID Drawings

This repo holds the results of a project that detects the large symbols on piping & instrumentation diagrams (P&IDs): **tanks, pumps and other major equipment** such as exchangers and vessels. Small symbols and line tracing are out of scope.

The model is a torchvision Faster R-CNN (ResNet-50-FPN v2, COCO-pretrained). It was trained on synthetic PID2Graph sheets and tested on 12 real OPEN100 sheets.

## Files

| File | What it is |
|---|---|
| `report.pdf` | 2-page progress report: methods, results with confidence intervals, limitations, sources |
| `results.ipynb` | Results notebook: per-class AP50, seed comparisons, predictions on outside drawings |
| `notebook.ipynb` | Main project notebook |

## Headline results (AP50, 12 real test sheets)

| Run | Tank | Pump | Equipment | mAP50 |
|---|---|---|---|---|
| Baseline (5-class model) | 0.26 | 0.02 | 0.06 | — |
| Large-equipment reference (3 seeds) | 0.60 | 0.01 | 0.07 | 0.23 |
| **T5 + T6 (3 seeds)** | **0.78** | **0.14** | **0.31** | **0.41** |

T5 adds sharp high-res zoom crops and stroke thinning. T6 adds copy-paste of equipment at real-sheet sizes. Pump detection is still weak. See `report.pdf` for details.
