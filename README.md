# Project Showcase: Multi-Source Alternative Data Syndication & Leak-Safe Asset Pricing Engine

An end-to-end quantitative data pipeline and predictive framework engineered in **R** to model urban real estate asset values by blending fundamental market data with alternative geospatial risk and demographic layers. 

This architecture is explicitly engineered to demonstrate the advanced data pipelines, unstructured text parsing, and rigorous validation hygiene required for institutional workflows.

**Headline result:** on 515 Boston listings, tuned XGBoost predicts price per square foot with a typical proportional error of roughly a quarter (RMSLE 0.25 under 5-fold cross-validation), against about a third for linear baselines. The full analysis is in [`Boston_Housing_Predictive_Framework.ipynb`](Boston_Housing_Predictive_Framework.ipynb), saved with its outputs so it can be read on GitHub without running anything.

---

## Core Architecture & Technical Highlights

### 1. Alternative Data Syndication
* **Multi-Source Ingestion:** Programmatically ingested and synthesized disparate alternative data sources across Boston, MA.
* **Layering Context:** Coupled core residential real estate assets with municipal public safety registries (historical crime densities) and localized U.S. Census Bureau demographic tracts.

### 2. Advanced Feature Engineering & Regular Expressions (Regex)
* **Unstructured Text Extraction:** Built robust text-parsing functions using regular expressions and lookarounds to isolate raw risk indices from heavily formatted string metadata.
* **Wide-Format Binarization:** Programmatically unpacked multi-valued, semi-colon-delimited asset attributes. By tokenizing vectors dynamically and binarizing them into wide-format sparse dummy matrices, the pipeline avoids structural row inflation while preserving modeling cleanliness.

### 3. Strict Leakage Controls & Custom Cross-Validation Wrapper
* **Feature Isolation:** Protected against look-ahead bias and circular dependencies by modelling log price per square foot while excluding the listing price and estimated monthly payment (both mechanically tied to the target: payment is about 0.64% of price, and price is the numerator) from every model's predictors. They are used only to score predictions.
* **Hierarchical In-Fold Imputation:** Operated a custom **5-Fold Cross-Validation** loop where missing data fields are resolved through a nested, 3-tiered cascading fallback scheme executed *entirely inside* the operational training partitions:
  $$\text{Property Type Medians} \longrightarrow \text{ZIP Code Medians} \longrightarrow \text{Global Medians}$$
* **Out-of-Sample Safeguards:** All statistical imputation parameters are derived exclusively from active training folds and dynamically mapped down to test partitions, ensuring zero look-ahead contamination.

### 4. Machine Learning Benchmarking & Explainability
* **Linear vs. Regularized Frameworks:** Benchmarked Ordinary Least Squares (OLS) baselines against penalized linear architectures (**Elastic Net**, $\alpha = 0.5$ blending Lasso and Ridge constraints) to handle sparse, high-dimensional features.
* **Non-Linear Tree Ensembles:** Deployed **Random Forest**, **XGBoost** and a hand-written **AdaBoost.R2** to capture non-linear effects such as that of building age, with hyperparameters selected by nested cross-validation.
* **Explainability Matrix:** Projected the complex ensemble variations back into a global, shallow CART decision tree (`rpart.plot`) to guarantee full transparency and interpretability of terminal node weightings.

### 5. Data Quality Controls
* **Merge Integrity:** The listings, police-incident and Census layers are joined on ZIP code with explicit checks: lookup tables must be unique per ZIP and a join may never add rows, otherwise the notebook stops.
* **Audit-Driven Filters:** An audit of the Boston listings led to three documented filters, each printing the number of rows it removes: non-housing units (parking spaces, boat slips, 0-bed/0-bath units under $200k), a listing entered twice under two ZIP codes, and rows where the monthly payment is inconsistent with the price (11 of 528 listings in total). In an earlier run with the payment as the target, removing them lowered XGBoost's RMSLE from about 0.38 to 0.25.

---

## Results

5-fold cross-validation, error on the log price-per-square-foot scale (RMSLE, roughly the typical proportional error):

| Model | RMSLE (fixed settings) | RMSLE (tuned) |
|---|---|---|
| OLS | 0.342 | n/a |
| Elastic Net | 0.338 | n/a |
| Random Forest | 0.253 | 0.251 |
| **XGBoost** | **0.253** | **0.247** |
| AdaBoost | 0.274 | 0.260 |

* The tree models clearly beat the linear baselines. Differences among the three tree models are small relative to fold-to-fold noise (standard deviation of roughly 0.02-0.03), and tuning adds little.
* Once size is removed from the target, neighbourhood features (walkability, school proximity, zip-level variables) carry as much predictive signal as the property's own features.
* The notebook also compares this target against modelling the monthly payment instead, and lists the limitations: a small sample (about 78% condos), asking prices at a single point in time, and zip-level features that cannot be separated from other neighbourhood effects.

---

## Data

All three files sit next to the notebook and are read by relative path.

| File | Contents | Source |
|---|---|---|
| `housing.csv` | Residential for-sale listings (8,983 rows; 529 in Boston with a ZIP code) | _Source and collection date to be added_ |
| `Boston_Incidents.csv` | Police incident reports (265,642 rows, about 56 MB) | _Source to be added_ |
| `DECENNIALDHC2020.P1-2026-10-19T233433.csv` | 2020 Decennial Census total population by ZIP code tabulation area (Massachusetts) | [data.census.gov](https://data.census.gov), table P1 |

## How to Run

You need R (4.x), Jupyter, and the R kernel:

```r
install.packages("IRkernel")
IRkernel::installspec()
```

Open the notebook in VS Code or JupyterLab, select the **R** kernel, and run all cells. The first cell installs any missing R packages. A full run takes roughly 15-20 minutes, almost all of it in the nested tuning section. The last code cell writes `FinalProjectDataset.csv`, the merged and cleaned dataset.

## Repository Contents

| File | Purpose |
|---|---|
| `Boston_Housing_Predictive_Framework.ipynb` | Main analysis |
| `housing.csv`, `Boston_Incidents.csv`, `DECENNIALDHC2020.P1-2026-10-19T233433.csv` | Input data |
