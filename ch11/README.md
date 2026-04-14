# Chapter 11 Data: Bayesian MTE Microsimulation

**Repository:** `higher-ed-policy-analysis-2nd-edition / data`  
**Path:** [`data/ch11`](https://github.com/higher-ed-policy-analysis-2nd-edition/data/tree/main/ch11)  
**Book:** *Higher Education Policy Analysis Using Quantitative Techniques*, Second Edition — Springer

---

## Overview

This directory contains the synthetic dataset used in **Chapter 11: Bayesian MTE Microsimulation — Cost–Benefit Analysis of a \$100k Lifetime Cap on Grad PLUS Loans**. The dataset is a B&B-mirroring synthetic panel of 8,000 graduate students designed to illustrate the marginal treatment effect (MTE) framework and Bayesian parametric-bootstrap cost–benefit analysis introduced in Chapter 10 and extended here to evaluate a federal loan-cap policy.

The simulated policy context is a **\$100,000 lifetime aggregate cap on federal Grad PLUS borrowing**, modeled after provisions of the *One Big Beautiful Bill* (Pub. L. 119-XX, effective July 1, 2026). Students whose cumulative Grad PLUS borrowing exceeds the cap face a binding credit constraint that may push them out of graduate enrollment. Chapter 11 uses the MTE framework to identify which students are displaced, estimate their counterfactual returns, and propagate coefficient uncertainty through 1,000 posterior draws to produce a full distribution of net social benefits, fiscal savings, and behavioral costs.

The data generation process (DGP) replicates the structural design of Chapter 10 (`Example_10_1.dta`), adding **Grad PLUS loan amounts** (`grad_plus_loans`) and **institutional revenue variables** (`annual_tuition`, `program_years`, `gross_tuition`, `net_inst_rev`) to support the institutional-impact arm of the CBA.

---

## Files in This Directory

| File | Description |
|------|-------------|
| `Example_11_1.dta` | Synthetic dataset — Stata format (compatible with Stata 14+) |
| `Example_11_1.csv` | Synthetic dataset — CSV format for use with R and other tools |

The companion analysis scripts are located in the code repository:

| Script | Repository path |
|--------|-----------------|
| `Stata_code11.do` | [`code/ch11/Stata_code11.do`](https://github.com/higher-ed-policy-analysis-2nd-edition/code/blob/main/ch11/Stata_code11.do) |
| `R_code11.R` | [`code/ch11/R_code11.R`](https://github.com/higher-ed-policy-analysis-2nd-edition/code/blob/main/ch11/R_code11.R) |

---

## Dataset: `Example_11_1`

### Dimensions

| Property | Value |
|----------|-------|
| Observations | 8,000 |
| Variables | 27 |
| Unit of analysis | Individual graduate student (synthetic) |
| Random seed | `20251201` |

### Variable Dictionary

#### Identifiers and Demographics

| Variable | Type | Description |
|----------|------|-------------|
| `id` | int | Student identifier (1–8,000) |
| `female` | binary | 1 = female (prevalence ≈ 52%) |
| `black` | binary | 1 = Black/African American (≈ 13%) |
| `hispanic` | binary | 1 = Hispanic/Latino (≈ 10%) |
| `asian` | binary | 1 = Asian (≈ 7%) |
| `age_ba` | int | Age at bachelor's degree completion; bounded [21, 35]; mean ≈ 24 |
| `firstgen` | binary | 1 = first-generation college student (≈ 26%) |
| `parent_income_q` | int | Parental income quartile (1 = lowest, 4 = highest) |
| `parent_grad` | binary | 1 = at least one parent holds a graduate degree (≈ 38%) |
| `ugpa` | float | Undergraduate GPA, rounded to two decimals; bounded [2.0, 4.0]; mean ≈ 3.3 |

#### Graduate Program Area

Four mutually exclusive binary indicators. The omitted base category is **Other / Social Sciences**.

| Variable | Type | Prevalence | Notes |
|----------|------|-----------|-------|
| `stem_major` | binary | ≈ 22% | Science, Technology, Engineering, Mathematics |
| `bus_major` | binary | ≈ 18% | Business / MBA |
| `ed_major` | binary | ≈ 16% | Education |
| `health_major` | binary | ≈ 14% | Health and related fields |

#### Institutional and State Characteristics

| Variable | Type | Description |
|----------|------|-------------|
| `selective_inst` | binary | 1 = selective institution (≈ 28%) |
| `public_ug` | binary | 1 = public undergraduate institution (≈ 72%) |
| `state_unemp` | float | State unemployment rate at enrollment (%); bounded [2.5, 10.5]; mean ≈ 5.2 |
| `metro` | binary | 1 = metropolitan area (≈ 68%) |

#### Instrument

| Variable | Type | Description |
|----------|------|-------------|
| `ga_funding_adj` | float | State need-based grant aid per FTE (\$000s, inflation-adjusted); bounded [2.0, 14.0]; mean ≈ 7.5. Serves as the excluded instrument in the probit selection model. Higher GA funding reduces loan dependence and raises completion propensity. |

#### Grad PLUS Loan Variables (Policy Exposure)

| Variable | Type | Description |
|----------|------|-------------|
| `grad_plus_loans` | float | Simulated lifetime Grad PLUS borrowing (\$000s); bounded [0, 250]. Depends on program type, institutional selectivity, family income, GA funding, and an individual-specific unobserved borrowing propensity. |

Loan amounts are constructed to reflect field-specific cost structures: Business/MBA programs carry the highest borrowing needs (mean ≈ \$65k), followed by STEM and Health programs; Education and Other programs are below the pooled mean. The \$100k policy cap binds for approximately 30–35% of completers in this synthetic sample.

#### Institutional Revenue Variables

| Variable | Type | Description |
|----------|------|-------------|
| `annual_tuition` | float | Annual graduate tuition (\$000s); floor at \$10k. Business ≈ \$60k/yr; STEM ≈ \$35k/yr; Education ≈ \$25k/yr. |
| `program_years` | float | Expected years to degree: 2.5 for STEM, 2.0 for all other programs. |
| `gross_tuition` | float | Gross tuition revenue per enrolled student = `annual_tuition` × `program_years` (\$000s). |
| `net_inst_rev` | float | Net institutional revenue per student = 65% of `gross_tuition` (\$000s). The 35% deduction represents variable instructional costs. An additional 20% of net revenue is assumed to cross-subsidize undergraduate aid and other programs, producing a multiplier effect on the institutional cost of cap-induced attrition. |

#### Outcome and Selection Variables

| Variable | Type | Description |
|----------|------|-------------|
| `masters` | binary | 1 = completed master's degree. Generated by a probit latent-index DGP with instrument `ga_funding_adj` and full covariate set. Approximately 50% of the sample completes. |
| `ln_salary` | float | Log annual salary (observed outcome). Constructed from heterogeneous potential outcomes under a true cubic MTE polynomial: MTE(*u*) = −2.50 + 19.30*u* − 30.25*u*² + 15.12*u*³, where *u* = 1 − P̂(*D*=1|*Z*,*X*). This functional form implies declining MTE (positive selection on gains) — students with the lowest unobserved resistance to completion have the largest returns. |
| `salary` | float | Annual salary in dollars = exp(`ln_salary`). |

---

## Data Generation Process

The dataset is fully synthetic. No real student records are used. The DGP is designed to replicate the structural properties of the Baccalaureate and Beyond (B&B) longitudinal surveys while incorporating policy-relevant variation in Grad PLUS borrowing.

Key design choices:

- **Selection equation.** Completion propensity is driven by GA funding (instrument), GPA, parental background, first-generation status, and field. The intercept is calibrated so that roughly 50% of the sample completes a master's degree.
- **Heterogeneous returns.** The true MTE polynomial has a single crossing of zero near *u* ≈ 0.30, implying that students with relatively low resistance gain from completion but students with high resistance do not. This shape is consistent with positive selection on gains and supports the identification of both behavioral costs (displaced high-return students) and efficiency gains (cap removing low-return borrowers).
- **Loan amounts.** Borrowing needs are tied to program-specific cost structures and family income, with individual-level noise. The \$100k cap is set at the 65th–70th percentile of the loan distribution among completers.
- **Institutional revenue.** Tuition schedules and cost-share parameters (65% net revenue rate, 20% cross-subsidy share) are calibrated to national averages from IPEDS revenue data.
- **Random seed.** Both the Stata script (`set seed 20251201`) and the R script (`set.seed(20251201)`) use the same seed. Because of differences between Stata's and R's pseudo-random number generators, the two reproductions produce datasets with the same distributional properties but not identical row-level values. The `.dta` file is the primary canonical dataset; the `.csv` is a saved copy of the same Stata-generated data.

---

## Figures Produced by the Analysis Scripts

| Figure | File (Stata) | File (R) | Description |
|--------|-------------|---------|-------------|
| Fig. 11.1 | `fig11_1_posterior_nb_Stata.png` | `fig11_1_posterior_nb_R.png` | Posterior distribution of net social benefits (\$000s); 1,000 draws |
| Fig. 11.2 | `fig11_2_mte_policy_Stata.png` | `fig11_2_mte_policy_R.png` | MTE curve (posterior mean) with policy-affected region (*u* ∈ [0.30, 0.65]) shaded |
| Fig. 11.3 | `fig11_3_cba_decomp_Stata.png` | `fig11_3_cba_decomp_R.png` | CBA decomposition — fiscal savings, efficiency gain, behavioral cost |
| Fig. 11.4 | `fig11_4_param_posteriors_Stata.png` | `fig11_4_param_posteriors_R.png` | Posterior densities of ATE, ATT, and ATU (illustrates ATU > ATT > ATE) |
| Fig. 11.5 | `fig11_5_mte_byfield_Stata.png` | `fig11_5_mte_byfield_R.png` | MTE curves by graduate program area (Business, STEM, Health, Education, Other) |
| Fig. 11.6 | `fig11_6_inst_rev_byfield_Stata.png` | `fig11_6_inst_rev_byfield_R.png` | Net institutional revenue loss by program area |
| Fig. 11.7 | `fig11_7_full_cost_stack_Stata.png` | `fig11_7_full_cost_stack_R.png` | Full social cost stack — student and institutional channels combined |

All figures are exported in grayscale at 1,200-pixel width (Stata) and saved as `.png` files in `Output/graphs/`. The R script produces equivalent figures using `theme_minimal()` with gray fill palettes for Springer print compatibility.

---

## Software Requirements

### Stata

- **Version:** Stata 19 or later
- **User-written packages:** None required
- All estimation uses built-in `probit`, `reg`, and `drawnorm` commands

### R

- **Version:** R 4.4 or later
- **Required packages:** `tidyverse`, `fixest`, `MASS`, `scales`, `broom`
- Install with: `install.packages(c("tidyverse", "fixest", "MASS", "scales", "broom"))`

---

## Cross-Chapter Dependencies

Chapter 11 builds directly on the MTE/MPRTE framework developed in **Chapter 10**. The following links are relevant:

- Chapter 10 data: [`data/ch10`](https://github.com/higher-ed-policy-analysis-2nd-edition/data/tree/main/ch10)
- Chapter 10 analysis scripts: [`code/ch10`](https://github.com/higher-ed-policy-analysis-2nd-edition/code/tree/main/ch10)
- The field-specific ATE differentials used to construct Figure 11.5 are taken from **Table 10.1** of the Chapter 10 output. The MTE interacted model (`mte_byarea`) estimated in Chapter 10 provides the field-level intercept offsets applied here (STEM: +0.134, Business: +0.885, Education: −0.165, Health: +0.038).
- The posterior for the MPRTE from Chapter 10 (`mte_bootstrap_prte_post.dta`) is imported as the prior for the Chapter 12 simulation-based CBA.

---

## Citation

> Titus, M. A. (forthcoming). *Higher Education Policy Analysis Using Quantitative Techniques* (2nd ed.). Springer.

Code development was assisted by Claude (Anthropic, 2025). The author provided all methodological specifications and reviewed, tested, and validated all code and output.

---

## License

Data are synthetic and released for educational use in connection with the book. See the repository root [`LICENSE`](https://github.com/higher-ed-policy-analysis-2nd-edition/data/blob/main/LICENSE) for terms.
