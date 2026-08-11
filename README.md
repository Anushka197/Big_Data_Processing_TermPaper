# Big Data Processing — Term Paper Study

## 📌 Study of Research Paper
> **"Integrating Vertical and Horizontal Partitioning into Automated Physical Database Design"**  
> *Sanjay Agrawal, Vivek Narasayya (Microsoft Research) & Beverly Yang (Stanford University)*  
> Published in **ACM SIGMOD 2004**

---

## Project Overview & Team Details
This repository contains a comprehensive study, detailed report analysis, and presentation conducted as part of the **Big Data Processing (BDP)** course term paper project.

* **Course**: Big Data Processing (BDP)
* **University**: Dhirubhai Ambani University (DAU / DA-IICT)
* **Submitted to**: Prof. P. M. Jat
* **Date**: November 5, 2025

### 👥 Team Members
* **Anushka Prajapati** (Student ID: `202301224`) — B.Tech ICT
* **Yashasvi Jadav** (Student ID: `202301069`) — B.Tech ICT

---

## 📁 Repository Contents

| File Name | Description |
| :--- | :--- |
| 📄 [TermPaper_Original.pdf](file:///d:/AnushkaData/DA%20life/Github_Projects/BDP_TermPaper/Big_Data_Processing_TermPaper/TermPaper_Original.pdf) | The original ACM SIGMOD 2004 research paper. |
| 📘 [BDP_202301069_202301224.pdf](file:///d:/AnushkaData/DA%20life/Github_Projects/BDP_TermPaper/Big_Data_Processing_TermPaper/BDP_202301069_202301224.pdf) | Our detailed term paper report and comprehensive analysis. |
| 📊 [Presentation_202301069_202301224.pdf](file:///d:/AnushkaData/DA%20life/Github_Projects/BDP_TermPaper/Big_Data_Processing_TermPaper/Presentation_202301069_202301224.pdf) | Slide deck used for presentation and evaluation review. |

---

## Key Concepts & What We Learned

### 1. Problem & Core Motivation
* **The Limitations of Staged Optimization**: Traditional physical database design tools optimized indexes and materialized views separately from partitioning. Staging these choices (e.g. picking un-partitioned indexes first, then horizontally/vertically partitioning) locks the database into suboptimal configurations, resulting in up to 30–40% execution slowdowns.
* **Integrated Optimization**: Physical design choices (indexes, materialized views, horizontal range/hash partitioning, and vertical sub-tables) interact strongly. They must be optimized jointly (`TOGETHER`).
* **Manageability & Alignment**: DBAs require aligned partitions—where indexes on a table share the exact same horizontal partitioning scheme as the underlying table—to streamline operational tasks like backup, restore, and maintenance.

---

### 2. Solution Architecture Overview
The proposed solution introduces a 4-stage optimizer-driven architecture:

```
┌───────────────────────────────────────────────────────────┐
│                    Workload & Database                    │
└─────────────────────────────┬─────────────────────────────┘
                              │
                              ▼
┌───────────────────────────────────────────────────────────┐
│  1. Column-Group Restriction                              │
│     • Prunes search space using CG-Cost(g) & VPC          │
└─────────────────────────────┬─────────────────────────────┘
                              │
                              ▼
┌───────────────────────────────────────────────────────────┐
│  2. Candidate Selection                                   │
│     • Per-query candidate generation using Greedy(m,k)    │
└─────────────────────────────┬─────────────────────────────┘
                              │
                              ▼
┌───────────────────────────────────────────────────────────┐
│  3. Merging                                               │
│     • Merges vertical & horizontal partitions             │
│     • Preserves co-location for join-heavy queries        │
└─────────────────────────────┬─────────────────────────────┘
                              │
                              ▼
┌───────────────────────────────────────────────────────────┐
│  4. Enumeration                                           │
│     • Global workload search                              │
│     • Lazy Enumeration for index/table alignment         │
└─────────────────────────────┬─────────────────────────────┘
                              │
                              ▼
┌───────────────────────────────────────────────────────────┐
│            Final Physical Design Recommendation           │
└───────────────────────────────────────────────────────────┘
```

#### Key Components:
1. **Column-Group Restriction (Pre-processing)**
   * **`CG-Cost(g)`**: Measures the fraction of workload cost referencing column-group `g`. Leverages monotonicity and frequent-itemset mining to prune unpromising column sets.
   * **`VPC` (Vertical Partitioning Confidence)**: Measures column co-occurrence across queries to prioritize vertical sub-tables that minimize join overheads.
2. **Candidate Selection**
   * Uses query optimizer "what-if" interfaces to cost candidates per query without physical execution, selecting top candidates using `Greedy(m,k)`.
3. **Merging**
   * Combines single-query candidates into generic multi-query structures.
   * Merges vertical partitions using sub-table unions and intersections.
   * Uses `MergeRanges` greedy interval merging for range partitioning and explicitly preserves **co-location** for efficient partition-wise joins.
4. **Enumeration & Lazy Alignment**
   * Instead of **Eager Enumeration** (which generates all aligned combinations upfront with quadratic complexity), **Lazy Enumeration** introduces aligned structures on-demand during the search when an unaligned structure is selected.
   * Achieves **90% runtime speedup** with $<1\%$ loss in recommendation quality.

---

### 3. Key Experimental Takeaways
* **Integrated (`TOGETHER`) vs. Staged (`VP->IND->HP`)**: Integrated optimization consistently outperformed index-only and staged approaches across all storage budgets.
* **Column-Group Pruning Efficiency**: Setting threshold $f = 0.02$ reduced tool running time by ~20% while incurring only ~2% quality loss.
* **Co-Location Impact**: Ignoring co-location during candidate merging resulted in up to an **80% drop in quality** on join-heavy workloads.

---

### 4. Future Research Directions & Reflections
* **Multi-Node Partitioning**: Extending single-node techniques to shared-nothing distributed clusters (accounting for network latency and data availability).
* **Cloud-Native Database Cost Models**: Modernizing cost estimation for cloud platforms (e.g., Snowflake, BigQuery, Redshift Spectrum) where compute and storage are decoupled and cost is driven by bytes scanned rather than disk I/O.
* **Machine Learning for Pruning**: Replacing heuristic metrics (`CG-Cost`, `VPC`) with ML models trained on query execution logs and plan trees for data-driven candidate selection.

---

## Citation
```bibtex
@inproceedings{agrawal2004integrating,
  author    = {Agrawal, Sanjay and Narasayya, Vivek and Yang, Beverly},
  title     = {Integrating Vertical and Horizontal Partitioning into Automated Physical Database Design},
  booktitle = {Proceedings of the 2004 ACM SIGMOD International Conference on Management of Data (SIGMOD '04)},
  pages     = {359--370},
  year      = {2004}
}
```
