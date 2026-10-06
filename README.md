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

## Sources

**Data**
- J. M. Stürmer, M. Graumann, T. Koch. *From Engineering Diagrams to Graphs: Digitizing P&IDs with Transformers* (PID2Graph). IEEE DSAA 2025. [arXiv:2411.13929](https://arxiv.org/abs/2411.13929). Dataset: [Zenodo record 14803338](https://zenodo.org/records/14803338). This is the source of the synthetic training and validation sheets and of the 12 real OPEN100 test sheets.
- The five outside drawings in `results.ipynb` (ethanol PFD, piping-diagram-2, OSHA PFD, processing-pid-rev, pid_big) are public example images found online. They have no ground-truth labels.

**Methods**
- S. Ren, K. He, R. Girshick, J. Sun. *Faster R-CNN: Towards Real-Time Object Detection with Region Proposal Networks.* NeurIPS 2015. [arXiv:1506.01497](https://arxiv.org/abs/1506.01497). This is the detector architecture.
- G. Ghiasi et al. *Simple Copy-Paste is a Strong Data Augmentation Method for Instance Segmentation.* CVPR 2021. [arXiv:2012.07177](https://arxiv.org/abs/2012.07177). The basis for T6 copy-paste.
- Prasad et al. *SynthPID.* CVPR 2026 Workshops. [arXiv:2604.16513](https://arxiv.org/abs/2604.16513). Informed the work on the gap between synthetic and real sheets (T5 stroke thinning).
- F. C. Akyon et al. *Slicing Aided Hyper Inference and Fine-tuning for Small Object Detection* (SAHI). ICIP 2022. [arXiv:2202.06934](https://arxiv.org/abs/2202.06934). Informed the zoom-crop training.
- M. Gupta, C. Wei, T. Czerniawski. *Semi-supervised symbol detection for piping and instrumentation drawings.* Automation in Construction 159, 2024. [Code](https://github.com/mgupta70/PID_Symbol_Detection). Informed the plan for real-sheet labels (T7).
- I. Robinson et al. *RF-DETR.* ICLR 2026. [arXiv:2511.09554](https://arxiv.org/abs/2511.09554). Considered as an alternative detector.

**Software**
- PyTorch / torchvision 0.29: [Faster R-CNN (ResNet-50-FPN v2)](https://docs.pytorch.org/vision/stable/models/faster_rcnn.html) and transforms v2.
- Lightning AI torchmetrics 1.9: [MeanAveragePrecision](https://lightning.ai/docs/torchmetrics/stable/detection/mean_average_precision.html), used for AP50 scoring.
- Training ran on the [National Research Platform (NRP) Nautilus](https://nationalresearchplatform.org/) GPU cluster.
