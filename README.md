# DA5401 Assignment 5:  Visualizing Data Veracity Challenges in Multi-Label Classification  

**Name**: Kaki Hephzi Sunanda<br>
**Roll Number**: DA25M015<br>
**Date**: 05.10.2025<br>

---

# Repository Information
This repository contains code for **manifold visualization and veracity inspection** on the **Yeast Genome** dataset.  
It demonstrates how gene-expression features are **standardized**, labels are **consolidated**, and structure is explored with **t-SNE** and **Isomap**.

---

# Repository Structure

```
📦 DA5401-Assignment5
├─ README.md
└─ DA5401_A5_DA25M015.ipynb
└─ plots/
```

---

# Dataset Information

## Dataset Information

- Gene expression profile of *Saccharomyces cerevisiae* (yeast)  
- **Number of Samples (rows):** 2,417 genes  
- **Total columns:** 117 = **103 features** + **14 label indicators**  
- **Feature source:** DNA microarray **expression levels** (diauxic shift, mitotic cell cycle, sporulation, temperature-reduction shocks) plus **phylogenetic profile**  
- **Labeling:** **14 first-level functional classes** (multi-label); a gene may belong to multiple classes  
- **Encoding note:** Label columns may load as byte strings (`b'0'`/`b'1'`).

---

# Work Done

## 1) Data Loading & Preparation
- Loaded the **ARFF** file into a **pandas DataFrame** and split columns into **103 features** and **14 labels**.
- Converted label indicators from **byte strings** (`b'0'` / `b'1'`) to **integers** (`int8`) for analysis.
- Standardized features with **StandardScaler** (zero mean, unit variance) to ensure **distance-based** methods are comparable.
- Verified scaling with a **global summary** (means $\approx$ 0, stds $\approx$ 1) to avoid scale-driven neighbor artifacts.

**Observations**
- The assignment states the dataset is **already standardized** thus the additional scaling keeps the pipeline **self-contained** and **reproducible**.
- Label columns are **multi-label binary indicators** across **14 first-level functional classes**.

---

## 2) Label Consolidation for Visualization
- Identified **single-label rows** where the row sum across 14 labels equals **1** (`single_mask`).
- Computed single-label frequencies and selected the **most frequent single-label class**.
- Ranked **unique multi-label combinations** with `value_counts()` and selected the **most frequent** combination (≥ 2 ones) as **TopMulti**.
- Built a simplified plotting target: **0 = Other**, **1 = SingleTop1**, **2 = SingleTop2 (if present)**, **3 = TopMulti**.

**Observations**
- Only **one** single-label class occurs (Class 1, **32 rows**); a **second** single-label bucket is **not present**.
- Found **198** distinct multi-label combinations and the top combination is highly **frequent**, indicating strong **co-annotation patterns**.

---

## 3) Manifold Visualizations
- Ran a **t-SNE** sweep over **perplexity** values `{5, 10, 15, 20, 30, 50}` on **scaled features**.
  ![partbq1](./plots/partbq1)
- Chose **perplexity = 30** for the final plot (balanced local/global emphasis, stable result for `n = 2417`).
 ![partbq2](./plots/partbq2)
- Produced **Isomap** embedding to compare **local vs. global** structure.
  [partcq1](./plots/partcq1)

**Observations**
- **t-SNE**: All perplexities show a **single mixed cloud**; colors (classes) are **interleaved**.
- **Isomap**: A **globally coherent** single manifold with **moderate curvature**; colors remain **mixed**.

---

## 4) Data Veracity Inspection
- **Noisy / Ambiguous labels**: Local neighborhoods contain **mixed colors**, consistent with **overlapping functions** and possible **annotation noise**.
- **Outliers**: A few **isolated points** appear on the fringes which may be the candidates for **unusual gene expression profiles** or **technical noise**.
- **Hard-to-learn samples**: Large regions have **low k-NN label purity** which suggests that simple models will **struggle** due to **overlapping** labels.

---
## Conclusion

- The analysis **standardized** features, **consolidated** labels for readable plots, and applied **t-SNE**, **PCA**, and **Isomap** to inspect structure.
- The dataset shows **intrinsic overlap** in which labels are **interleaved** across the manifold despite careful scaling and tuning.
- Only **one** single-label class appears (Class 1 with 32 rows). The second **single-label** bucket is **absent**.
- There are **many** multi-label combinations (198 distinct), and the **TopMulti** pattern is **highly frequent**, confirming strong **co-annotation**.
- **t-SNE** with **perplexity = 30** offers a **stable** and **balanced** view and varying perplexity does **not** reveal clean class separation.
- **Isomap** yields a **globally coherent**, single manifold with **moderate curvature**, yet colors remain **mixed**.
- The findings support that the difficulty is **data-driven** (multi-label biology, class imbalance, heterogeneous “Other”), not a failure of **scaling** or **parameter choice**.

### Key Takeaways
- The plots faithfully show that labels overlap on a **shared manifold**.
- Successful classifiers will need **non-linear boundaries** and **label-correlation** handling. Simple linear or distance-only rules will **struggle** with this data.
