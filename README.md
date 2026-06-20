# Undergraduate Thesis — Jamal Hossain

**Topic:** Interactive 3D Decision Space Visualization for Misclassification Analysis in Machine Learning–Driven Network Intrusion Detection Systems

**Institution:** Dept. of Computer Science and Engineering, University of Rajshahi, Bangladesh


This repository documents the full lifecycle of my undergraduate thesis — from pre-defense exploration through final defense — culminating in a peer-reviewed paper under review at the **29th International Conference on Computer and Information Technology(ICCIT)**, December 18-20, 2026  Cox’s Bazar, Bangladesh.

> 📄 **Paper:** *An Interactive 3D Decision Space Visualization Framework for Misclassification Analysis in Machine Learning Driven Network Intrusion Detection Systems*
> **Authors:** Jamal Hossain, Mazharul Islam, Krishna Debnath

---

## Abstract—Machine learning has improved the performance
of Network Intrusion Detection Systems in cybersecurity, but
many models still provide limited insight into their classification
errors. Standard evaluation measures such as accuracy, precision,
recall, F1 score, and confusion matrices summarize performance,
but they do not show where misclassified samples lie in the
feature space or how decision boundaries behave around rare
attack classes. Although SHAP and LIME can explain feature
contributions, they do not directly visualize the geometric position
of misclassified samples within classifier decision regions. To
overcome this limitation, this study proposes an interactive
three dimensional framework that visualizes decision spaces and
supports misclassification analysis in machine learning driven
Network Intrusion Detection Systems. The framework applies
PCA for variance based reduction and LDA for class discrim
ination, then renders the projected decision spaces as WebGL
enabled three dimensional scenes. A voxel based representation
with 1503 grid cells is used to examine geometric patterns behind
classification errors. Eight base classifiers and five ensemble
configurations are evaluated on UNSW NB15, NSL KDD, and
an additional diagnostic NSL KDD Merge setting using five
fold stratified cross validation. The 100 percent accuracy on
UNSW NB15 is analyzed through the projected decision space,
where training and testing samples form highly similar and well
separated clusters, indicating geometric class separability rather
than data leakage or overfitting. On NSL KDD, low R2L recall
is linked to weak class separability and train test distribution
shift. In the NSL KDD Merge setting, Random Forest, Weighted
Hard Voting, and Stacking achieve 99.823% accuracy. Overall,
the framework connects statistical evaluation with visual decision
space evidence to help cybersecurity analysts understand where
and why machine learning based intrusion detection models fail.
Index Terms—Cybersecurity, Network Intrusion Detection, 3D
Decision Space Visualization, Decision Boundary, Misclassifica
tion Analysis, Ensemble Learning, PCA, LDA, UNSW-NB15,
NSL-KDD
---

## Pipeline

<img src="figures/pipeline.png" width="500">

**Stages:**
1. **Preprocessing** — missing-value imputation, one-hot encoding, min-max normalization, zero-mean centering. All fit on the training partition only, to prevent leakage.
2. **Feature Selection** — Information Gain weighting down-weights low-relevance dimensions before reduction.
3. **Dimensionality Reduction**
   - **PCA** (variance-based): first 13 principal components retained — 96.3% variance on UNSW-NB15, 98.7% on NSL-KDD. First 3 components drive the 3D rendering.
   - **LDA** (class-discriminative): applied independently on the 13D PCA output, maximizing between-class vs. within-class scatter. Trains KNN and all ensemble models.
4. **Model Training** — 8 base classifiers: AdaBoost, Bagging, CatBoost, Gradient Boosting, KNN, Random Forest, SVM (RBF), XGBoost. 5 ensembles: Hard/Soft/Weighted-Hard/Weighted-Soft Voting, and Stacking (Logistic Regression meta-learner). All hyperparameters selected via 5-fold stratified cross-validation.
5. **3D Visualization** — a **150³ (≈3.375M) voxel grid** materializes the decision surface: each voxel is extended to 13D, classified, and colored by predicted class. Rendered as an interactive, rotatable WebGL scene with 6-DOF navigation, boundary toggling, and hover-to-inspect.
6. **Evaluation** — Accuracy, F1, Precision, Recall, cross-referenced against the visual decision space to interpret *why* a model scored the way it did.

---

## Datasets

| Dataset | Train | Test | Features | Challenge |
|---|---|---|---|---|
| **UNSW-NB15** | 82,332 | 175,342 | 45 | Moderate imbalance; 8 attack categories |
| **NSL-KDD** | 125,973 | 22,544 | 42 | Severe imbalance — R2L: 995 samples, U2R: 52 samples |

SMOTE and SMOTE+Tomek oversampling applied during training to address class imbalance.

---

## Visualization Results

### UNSW-NB15: Train vs. Test Cluster Geometry

<img src="figures/un_5.1.png" width="500">

Training and test splits, viewed from two camera angles, occupy nearly identical 3D regions — the geometric explanation for why several classifiers reach **100% accuracy** on this dataset. The result reflects genuine class separability, not overfitting or data leakage.

### UNSW-NB15: Per-Class Cluster Examples

<img src="figures/un_5.2.png" width="500">

Normal traffic clusters tightly and consistently across train/test (explaining high Normal precision). Generic attacks form an even tighter, isolated cluster (explaining high recall). Mixed-traffic regions shared by multiple classes are the direct geometric source of cross-class confusion.

### UNSW-NB15: Misclassification Close-Up

<img src="figures/un_5.5.png" width="500">

A zoomed view of the Normal/Abnormal boundary. 1,010 Abnormal instances are geometrically co-located with Normal traffic in the first three principal components — visually confirming why they're misclassified, and showing that no amount of threshold tuning alone would fix it without new features.

### UNSW-NB15: Per-Model Decision Spaces

<img src="figures/un_model.png" width="500">

Random Forest and KNN produce sharply bounded decision regions (consistent with their near-100% accuracy). AdaBoost and CatBoost produce broader, less confident surfaces. Bagging shows the most diffuse boundaries of any base classifier — directly visible, not just inferable from a metrics table.

### UNSW-NB15: Ensemble Decision Spaces

<img src="figures/en_un.png" width="500">

The Voting Classifier consolidates heterogeneous base learners into broad, unified boundaries. Stacking produces visibly tighter per-class surfaces, reflecting the meta-learner's exploitation of complementary base-model strengths.

### NSL-KDD: Train vs. Test Distribution Shift

<img src="figures/ns_5.1.png" width="500">

Unlike UNSW-NB15, NSL-KDD's train and test splits diverge visibly — especially in the R2L/Normal region. This is the direct geometric cause of the **5.6% R2L recall**: a distribution shift the model never saw during training.

### NSL-KDD: Per-Class Cluster Examples

<img src="figures/ns_5.3.png" width="500">

DoS traffic is isolated and homogeneous (high recall). The critical finding is in the R2L panels: in training, that cluster region is mostly Normal traffic; in test, R2L samples densely co-locate in the *same* region — so the classifier defaults to the dominant Normal label. This shift is invisible in summary statistics but immediately obvious in 3D.

### NSL-KDD: Multi-Category Decision Spaces

<img src="figures/ns_5.5.png" width="500">

DoS occupies a well-defined, isolated region (91.9% recall). Normal occupies the largest volumetric region. R2L's decision space is sparse and fragmented — visual confirmation that the classifier cannot maintain a coherent boundary given the underlying distribution shift.

### NSL-KDD: Ensemble Decision Spaces

<img src="figures/en_ns.png" width="500">

The Voting Classifier produces smooth, broad boundaries that reduce false positives at the Normal/DoS border. Stacking produces tighter per-class surfaces with measurably improved Probe and R2L coverage.

---

## Results Summary

### Best Models (5-fold stratified CV)

| Model | UNSW-NB15 Acc. | UNSW-NB15 F1 | NSL-KDD Acc. | NSL-KDD F1 |
|---|---|---|---|---|
| KNN | **100.0%** | **100.0%** | 91.44% | 69.94% |
| Random Forest | 99.25% | 99.15% | 91.79% | 70.74% |
| XGBoost | 99.97% | 99.96% | **92.18%** | 70.11% |
| CatBoost | 99.99% | 99.99% | 88.27% | 55.24% |
| AdaBoost | 99.09% | 98.96% | 86.06% | 51.95% |

### Diagnostic: NSL-KDD Merge Setting

To isolate the effect of distribution shift, the original train and test files were concatenated and re-split 75:25.

| Model | Standard NSL-KDD | NSL-KDD Merge |
|---|---|---|
| Random Forest | 91.79% | **99.823%** |
| Weighted-Hard Voting | 91.83% | **99.823%** |
| Stacking | 91.90% | **99.823%** |
| AdaBoost | 86.06% | 90.28% |

The convergence to 99.823% across multiple models — and AdaBoost's ~4-point jump — confirms that low R2L recall on standard NSL-KDD is a **distribution-shift problem visible in the decision space**, not a fundamental modeling limitation.

---

## Repository Structure

```
.
├── PreDefense/                          # Pre-defense exploration, drafts, early experiments
├── Dataset Analysis/                    # EDA, class distributions, preprocessing notebooks
├── 3D Visualization/                    # PCA/LDA pipeline + voxel-grid WebGL rendering code
├── Final ML Model Results/              # Trained classifier outputs, metrics, confusion matrices
├── Final Report and Presentation Slide/ # Defended thesis report + defense slides
├── figures/                             # Paper figures (pipeline + all decision-space renders)
└── README.md
```

---

## Citation

```bibtex
@inproceedings{hossain2026interactive,
  title={An Interactive 3D Decision Space Visualization Framework for Misclassification Analysis in Machine Learning Driven Network Intrusion Detection Systems},
  author={Hossain, Jamal and Islam, Mazharul and Debnath, Krishna},
  booktitle={2026 IEEE 2nd International Conference on Quantum Photonics, Artificial Intelligence, and Networking (QPAIN)},
  year={2026},
  address={Chittagong, Bangladesh}
}
```

---

## Future Work

- Replace PCA with variational autoencoders to capture non-linear manifold structure
- Adapt the framework for live traffic analysis via incremental PCA updates
- Extend evaluation to IoT-specific benchmarks (TON_IoT, N-BaIoT)

---

## Contact

Jamal Hossain — jamal37.ru.cse@gmail.com
