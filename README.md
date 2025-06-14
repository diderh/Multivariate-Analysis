Methodology:
1. Data Sources
environment.rds: Environmental variables per site (e.g., temperature, altitude, cat_area, max_depth, conductivity, pH).
macroinvertebrates.rds: Macroinvertebrate species abundance data per site (each species as a column, presence/absence or abundance as values).
species_final.csv: Species abundance (used for some diversity and multivariate analyses).
Species_Final_FD.csv: Functional diversity traits for macroinvertebrates.
trait_Final_FD.csv: Detailed biological traits for macroinvertebrate species.
2. Data Frames and Key Variables
environment

Columns: sample_id, temperature, cat_area, altitude, max_depth, conductivity, pH, etc.
macroinvertebrates

Columns: sample_id, Haliplus sp., Baetis sp., ... (each species as a column)
agg_data (merged data)

Created by merging environment and macroinvertebrates on sample_id.
Contains: All environmental variables and all species columns.
Transformed columns: log-transformed versions of environmental variables (e.g., Cat_Area, Altitude, Max_Depth, Conductivity, log_pH).
trans.var

Contains selected/processed (including log-transformed) environmental variables:
temperature, Cat_Area, Altitude, Max_Depth, Conductivity, log_pH
species_ex

Data frame with species abundance per sample site (from CSV).
trait_macro / trait_macro2

Biological/functional traits data for each macroinvertebrate species.
Columns: feed_single, resp_single, volt_single, locom_single, ovip_single, etc.
trait_macro2: Row names set to species name, trait columns only.
3. Data Processing Steps
Read in environmental and abundance data.
Merge on sample_id to form agg_data.
Log-transform skewed environmental variables.
Remove NA/infinite values after transformation.
Create trans.var for statistical analysis with selected variables.
Diversity metrics & richness calculated on species abundance data.
GLM/CCA/RLQ analyses relate environmental variables, species abundance, and functional traits.
4. Analytical Workflow
Summary statistics: On environmental variables (raw and transformed)
GLM: Models abundance of individual taxa (e.g., Haliplus sp.) as a function of environmental variables.
CCA (Canonical Correspondence Analysis): Relates multivariate species abundance to environmental gradients.
RLQ analysis: Relates environment, species traits, and abundance.
5. Output
Statistical summaries (tables, VIFs, permutation tests, etc.)
Diversity indices (species richness, Shannon index)
Plots (boxplots, biplots, triplots, correlation plots)
Interpretation and discussion of environmental effects on species and traits.
Entity-Relationship Summary
Site (sample_id): Primary key; links all data.
Environment Variables: Linked to site.
Species Abundance: Each species is a variable, linked to site.
Functional Traits: Linked to species.
In summary:

The schema is centered around sample sites (sample_id), with environmental data and species data merged.
Environmental variables are preprocessed (log-transformed).
Analyses explore the relationship between environmental variables, species abundance, and functional traits.
Results are presented as statistical tables and visualizations.


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

