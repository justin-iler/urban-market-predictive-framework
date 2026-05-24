Multi-Source Alternative Data Syndication & Leak-Safe Asset Pricing Engine
I have developed an end-to-end quantitative data pipeline and predictive framework in R engineered to model urban real estate asset values by blending fundamental market data with alternative geospatial risk and demographic layers.
This project is explicitly architected to demonstrate advanced data engineering capabilities, unstructured text parsing, and rigorous validation hygiene required for institutional Quantitative Research and Location Analytics workflows.

Core Architecture & Technical Highlights:
Alternative Data Syndication: Programmatically ingested and combined disparate alternative data sources—coupling real estate assets across the Boston MSA with municipal public safety registries (historical crime densities) and localized U.S. Census Bureau demographic tracts.
Advanced Feature Munging & Regex Layers: Built text-parsing features using complex regular expressions and lookarounds to isolate raw risk indices from formatted metadata strings. Handled messy, multi-valued, semi-colon-delimited asset attributes by tokenizing vectors dynamically and binarizing them into wide-format sparse dummy matrices without inducing structural row inflation.
Strict Leakage Controls & Custom CV Wrapper: To protect against backtest optimization and look-ahead bias, the pipeline isolates endogenous variables (dropping market price-per-square-foot ratios to eliminate circular dependencies). It operates a custom 5-Fold Cross-Validation loop where missing values are resolved through a nested, 3-tiered hierarchical fallback imputation scheme:
Property Type Medians⟶ZIP Code Medians⟶Global Training Medians
All statistical parameters are derived exclusively from active training folds and dynamically mapped down to test partitions.
Machine Learning Benchmarking & Diagnostics: Evaluated Ordinary Least Squares (OLS) baselines against penalized linear models (Elastic Net, α=0.5 blending Lasso and Ridge constraints) to handle sparse, high-dimensional features. Implemented non-linear tree ensembles (Random Forest) to map non-linear spatial decay curves (e.g., the quadratic decay of property age on pricing matrices).
Model Explainability: Projected the ensemble variations into a global, shallow CART decision structure (rpart.plot) to guarantee full interpretability of terminal node weightings.
