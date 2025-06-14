Key methodological components include:

Data Preprocessing: Merging species abundance and environmental datasets, log-transforming skewed variables (catchment area, altitude, depth, conductivity, pH), and handling missing values.

Diversity Metrics: Calculation of species richness (using specnumber()) and Shannon-Weaver diversity indices across 327 sampling sites.

Generalized Linear Models (GLM): Stepwise backward regression to identify significant environmental predictors (e.g., temperature, conductivity) for target taxa (Haliplus sp.), with multicollinearity checks via Variance Inflation Factor (VIF).

Canonical Correspondence Analysis (CCA): Ordination to visualize species-environment relationships, followed by permutation tests (999 iterations) and PERMANOVA to validate significance.

RLQ Analysis: Trait-based analysis linking environmental variables (R), species abundance (L), and biological traits (Q) using principal component and correspondence analyses.

The script generates publication-ready figures (e.g., correlation plots, CCA triplots) and tables, with dynamic rendering for both HTML and Word outputs. All analyses leverage R packages including vegan, ade4, ggplot2, and kableExtra. File paths are configured for local reproducibility, and citations follow APA style via BibTeX.
