# Project Showcase: Multi-Source Alternative Data Syndication & Leak-Safe Asset Pricing Engine

An end-to-end quantitative data pipeline and predictive framework engineered in **R** to model urban real estate asset values by blending fundamental market data with alternative geospatial risk and demographic layers. 

This architecture is explicitly engineered to demonstrate the advanced data pipelines, unstructured text parsing, and rigorous validation hygiene required for institutional **Quantitative Research (Hedge Funds)** and **Location Analytics (Supply Chain)** workflows.

---

## Core Architecture & Technical Highlights

### 1. Alternative Data Syndication
* **Multi-Source Ingestion:** Programmatically ingested and synthesized disparate alternative data sources across the Boston MSA.
* **Layering Context:** Coupled core residential real estate assets with municipal public safety registries (historical crime densities) and localized U.S. Census Bureau demographic tracts.

### 2. Advanced Feature Engineering & Regular Expressions (Regex)
* **Unstructured Text Extraction:** Built robust text-parsing functions using regular expressions and lookarounds to isolate raw risk indices from heavily formatted string metadata.
* **Wide-Format Binarization:** Programmatically unpacked multi-valued, semi-colon-delimited asset attributes. By tokenizing vectors dynamically and binarizing them into wide-format sparse dummy matrices, the pipeline avoids structural row inflation while preserving modeling cleanliness.

### 3. Strict Leakage Controls & Custom Cross-Validation Wrapper
* **Feature Isolation:** Protected against backtest optimization and look-ahead bias by completely isolating endogenous variables—dropping raw market price-per-square-foot ratios to eliminate circular mathematical dependencies.
* **Hierarchical In-Fold Imputation:** Operated a custom **5-Fold Cross-Validation** loop where missing data fields are resolved through a nested, 3-tiered cascading fallback scheme executed *entirely inside* the operational training partitions:
  $$\text{Property Type Medians} \longrightarrow \text{ZIP Code Medians} \longrightarrow \text{Global Medians}$$
* **Out-of-Sample Safeguards:** All statistical imputation parameters are derived exclusively from active training folds and dynamically mapped down to test partitions, ensuring zero look-ahead contamination.

### 4. Machine Learning Benchmarking & Explainability
* **Linear vs. Regularized Frameworks:** Benchmarked Ordinary Least Squares (OLS) baselines against penalized linear architectures (**Elastic Net**, $\alpha = 0.5$ blending Lasso and Ridge constraints) to handle sparse, high-dimensional features.
* **Non-Linear Tree Ensembles:** Deployed a tuned **Random Forest** network to automatically map non-linear spatial decay curves (such as the quadratic decay effect of property age on real estate pricing matrices).
* **Explainability Matrix:** Projected the complex ensemble variations back into a global, shallow CART decision tree (`rpart.plot`) to guarantee full transparency and interpretability of terminal node weightings.
