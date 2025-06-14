
# Multivariate Analysis of Macroinvertebrate Communities

## Table of Contents

- [Project Overview](#project-overview)
- [Getting Started](#getting-started)
- [Workflow Steps](#workflow-steps)
- [Outputs](#outputs)
- [Conclusion](#conclusion)

---

## Project Overview

This project provides a comprehensive workflow for analyzing macroinvertebrate community structure in relation to environmental gradients and biological traits. The analysis leverages rich datasets, including environmental variables, species abundance, and functional traits, to perform advanced multivariate analyses such as GLM, CCA, and RLQ.

**Key Data Sources:**
- `environment.rds`: Environmental variables per site (e.g., temperature, altitude, cat_area, max_depth, conductivity, pH).
- `macroinvertebrates.rds`: Abundance/presence data for macroinvertebrate species per site.
- `species_final.csv`: Species abundance data for certain analyses.
- `Species_Final_FD.csv`: Macroinvertebrate functional diversity traits.
- `trait_Final_FD.csv`: Detailed biological traits for macroinvertebrate species.

**Schema Summary:**
- Data are centered on `sample_id`, linking environmental and biological data.
- Environmental variables are preprocessed (e.g., log-transformed).
- Analyses explore links between environment, species abundance, and traits.

---

## Getting Started

1. **Clone the repository** and ensure you have R and the following packages installed:
    - `tidyverse`
    - `vegan`
    - `ade4`
    - `FD`
2. **Data Preparation:**
    - Place all data files (`environment.rds`, `macroinvertebrates.rds`, `species_final.csv`, `Species_Final_FD.csv`, `trait_Final_FD.csv`) in your working directory.
3. **Open your R environment** and load the data:

    ```r
    environment <- readRDS("environment.rds")
    macroinv <- readRDS("macroinvertebrates.rds")
    species_abund <- read.csv("species_final.csv", row.names = 1)
    traits <- read.csv("trait_Final_FD.csv", row.names = 1)
    ```

---

## Workflow Steps

### 1. Data Processing

- Merge environmental and macroinvertebrate data by `sample_id`.
- Log-transform skewed variables (e.g., cat_area, altitude, max_depth, conductivity, pH).
- Remove NA or infinite values.
- Create selected variable sets for statistical analyses.

### 2. Diversity Analysis

- Calculate species richness and diversity indices (e.g., Shannon index) on abundance data.

### 3. Statistical Modeling

- **GLM:** Model abundance of individual taxa (e.g., Haliplus sp.) as a function of environmental variables.
- **CCA:** Relate multivariate species abundance to environmental gradients.
- **RLQ:** Integrate environmental variables, species abundance, and functional traits for trait-environment relationships.

### 4. Schema & Relationships

- **sites**: Primary key is `sample_id`.
- **environment**: Environmental variables, linked by `sample_id`.
- **macroinvertebrates**: Abundance data, linked by `sample_id`.
- **species_traits**: Biological/functional traits, linked by `species_name`.
- **agg_data**: Merged table/view for analysis.

---

## Outputs

- Summary statistics and statistical test outputs (tables, VIFs, permutation tests).
- Diversity indices (species richness, Shannon index).
- Visualizations: boxplots, CCA biplots, RLQ triplots, correlation plots.
- Interpretation and discussion of environmental effects on macroinvertebrate species and traits.

---

## Conclusion

This repository provides a structured approach to analyzing macroinvertebrate communities and their environmental and functional trait drivers. The workflow is reproducible and adaptable for similar ecological datasets.  
For feedback, questions, or contributions, please open an issue or contact the maintainer.

---






## Database-Style Schema for Multivariate Macroinvertebrate Analysis

### Table: sites

| Column Name | Data Type | Description                        | Key        |
|-------------|-----------|------------------------------------|------------|
| sample_id   | string    | Unique identifier for each site    | Primary    |
| ...         | ...       | Other site metadata (if any)       |            |


### Table: environment

| Column Name       | Data Type | Description                                | Key         |
|-------------------|-----------|--------------------------------------------|-------------|
| sample_id         | string    | Foreign key to sites                       | Foreign     |
| temperature       | numeric   | Water temperature                          |             |
| cat_area          | numeric   | Catchment area                             |             |
| altitude          | numeric   | Site altitude                              |             |
| max_depth         | numeric   | Maximum water depth                        |             |
| conductivity      | numeric   | Conductivity of water                      |             |
| pH                | numeric   | pH value                                   |             |
| ...               | ...       | Other environmental variables              |             |


### Table: macroinvertebrates

| Column Name           | Data Type | Description                              | Key         |
|-----------------------|-----------|------------------------------------------|-------------|
| sample_id             | string    | Foreign key to sites                     | Foreign     |
| Haliplus_sp           | integer   | Abundance or presence/absence            |             |
| Baetis_sp             | integer   | Abundance or presence/absence            |             |
| ...                   | ...       | Other species columns                    |             |


### Table: agg_data  (merged environment + macroinvertebrates)

| Column Name         | Data Type | Description                                | Key         |
|---------------------|-----------|--------------------------------------------|-------------|
| sample_id           | string    | Unique identifier; FK to sites             | Primary     |
| temperature         | numeric   | Inherited from environment                 |             |
| cat_area            | numeric   | Inherited from environment                 |             |
| Cat_Area            | numeric   | Log-transformed cat_area                   |             |
| altitude            | numeric   | Inherited from environment                 |             |
| Altitude            | numeric   | Log-transformed altitude                   |             |
| max_depth           | numeric   | Inherited from environment                 |             |
| Max_Depth           | numeric   | Log-transformed max_depth                  |             |
| conductivity        | numeric   | Inherited from environment                 |             |
| Conductivity        | numeric   | Log-transformed conductivity               |             |
| pH                  | numeric   | Inherited from environment                 |             |
| log_pH              | numeric   | Log-transformed pH                        |             |
| [species columns]   | integer   | Abundance/presence for each species        |             |
| ...                 | ...       | Other merged variables                     |             |
| (Note: columns from both environment and macroinvertebrates, plus transformations) | | | |


### Table: species_traits

| Column Name      | Data Type | Description                                  | Key         |
|------------------|-----------|----------------------------------------------|-------------|
| species_name     | string    | Unique identifier for each species           | Primary     |
| feed_single      | integer   | Feeding trait code (as number)               |             |
| resp_single      | integer   | Respiration trait code (as number)           |             |
| volt_single      | integer   | Voltinism trait code (as number)             |             |
| locom_single     | integer   | Locomotion trait code (as number)            |             |
| ovip_single      | integer   | Oviposition trait code (as number)           |             |
| ...              | ...       | Other biological trait variables             |             |


### Table: species_abundance

| Column Name      | Data Type | Description                                  | Key         |
|------------------|-----------|----------------------------------------------|-------------|
| sample_id        | string    | Foreign key to sites                         | Foreign     |
| species_name     | string    | Foreign key to species_traits                | Foreign     |
| abundance        | integer   | Count or 0/1 for presence/absence            |             |


### Table: transformed_env

| Column Name      | Data Type | Description                                  | Key         |
|------------------|-----------|----------------------------------------------|-------------|
| sample_id        | string    | Foreign key to sites                         | Foreign     |
| temperature      | numeric   | Raw value                                    |             |
| Cat_Area         | numeric   | Log-transformed                              |             |
| Altitude         | numeric   | Log-transformed                              |             |
| Max_Depth        | numeric   | Log-transformed                              |             |
| Conductivity     | numeric   | Log-transformed                              |             |
| log_pH           | numeric   | Log-transformed                              |             |


---

### **Entity-Relationship Diagram (Textual)**

- **sites** (1) — (N) **environment**
- **sites** (1) — (N) **macroinvertebrates**
- **sites** (1) — (N) **species_abundance**
- **species_traits** (1) — (N) **species_abundance**
- **agg_data** is a view or merged table: environment JOIN macroinvertebrates ON sample_id
- **transformed_env** is a view/table with log-transformed environmental variables

---

**Notes:**
- All tables can be joined on `sample_id` (site) or `species_name` (species-level).
- The `traits` table can be joined with any abundance table on `species_name` for trait-based analyses.
- Log-transformed variables are used for statistical modeling.
- `agg_data` is the main working data frame for multivariate analyses.

