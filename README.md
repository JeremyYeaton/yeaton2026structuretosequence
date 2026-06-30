# Multinomial Processing Tree model of syntactic encoding

This repository contains the data and analysis code accompanying:

> Yeaton, J. D., Walker, G., Fahey, D., Matchin, W., Fridriksson, J., & Hickok, G. (2026). From structure to sequence: A multinomial processing tree model of syntactic encoding. *Psychonomic Bulletin & Review*. https://doi.org/[DOI]
>
> Preprint: https://doi.org/10.31234/osf.io/w53f9_v2

Developed with R version 4.3.1 (2023-06-16) and JAGS 4.3.0 on macOS Sequoia 15.4.1. See session_info.txt and renv.lock for full dependency details.

---

## Repository Structure

```
.
├── data/
│   └── yeaton2026_data_deidentified.csv   # De-identified count matrix (see below)
├── models/
│   ├── mpt_model.txt             # JAGS model: Multinomial Processing Tree
│   └── null_model.txt            # JAGS model: Null multinomial (baseline)
├── figures/                     # Output directory, created automatically
├── mpt_Syntax_analysis.R          # Main analysis script
└── README.md
```

---

## Data

`data/yeaton2026_data_deidentified.csv` contains pre-processed, de-identified utterance
counts with one row per participant (N = 79) and one column per response type:

| Column | Description |
|--------|-------------|
| `G`  | Grammatical utterances |
| `H`  | Hierarchical errors |
| `L`  | Linearization errors |
| `HL` | Mixed (hierarchical + linear) errors |
| `O`  | Omissions |

Participants are identified only by an anonymous index (`S 1` – `S 79`). The original
utterance-level data cannot be shared to protect participant privacy. The count matrix
was derived from those data by: excluding hesitation utterances, excluding participants
with ≤ 9 utterances, and tallying utterances into the five response categories above
(see paper for full recoding details).

---

## Models

### MPT model (`models/mpt_model.txt`)

A Multinomial Processing Tree (MPT) model in which each subject's error profile is
explained by three latent binary processing stages:

- **a**: probability of successfully applying hierarchical syntactic structure
- **b**: probability of successfully applying linear syntactic structure  
- **c**: probability of overt production (vs. omission)

Higher values indicate better preserved ability. The five response-type probabilities
(G, H, L, HL, O) are deterministic functions of these three parameters. Each parameter
is estimated on the probit scale with a standard normal prior and transformed via φ.

### Null model (`models/null_model.txt`)

A baseline multinomial model with four free latent parameters per subject (stick-breaking
construction), one per response category (the fifth is constrained to sum to 1). This
model makes no structural assumptions about the relationship between categories and serves
as a point of comparison for DIC.

---

## Running the Analysis

1. Install required R packages:

```r
install.packages(c("tidyverse", "R2jags", "ggpubr", "patchwork"))
```

2. Install JAGS: https://mcmc-jags.sourceforge.io

3. Set the paths at the top of `mpt_Syntax_analysis.R` if needed (defaults assume the
   directory structure above).

4. Run the script. MCMC sampling (4 chains × 2,500 iterations) takes approximately
   [X] minutes on a standard desktop.

---

## Outputs

| File | Description |
|------|-------------|
| `figures/mpt_abilities.jpg` | MPT ability estimates vs. observed error rates (3×3 grid, Figure X in paper) |
| `figures/mpt_obs_post_cor.jpg` | Observed vs. posterior predictive counts for both models |

DIC values and posterior predictive fit statistics (R², Bonferroni-corrected p-values)
are printed to the console.

---

## MCMC Settings

| Parameter | Value |
|-----------|-------|
| Chains | 4 |
| Burn-in | 100 |
| Posterior samples | 2,500 |
| Thinning | 1 |

Convergence should be assessed via R-hat (all parameters should have R-hat < 1.1;
ideally < 1.01). These are printed as part of `print(mpt_output)`.