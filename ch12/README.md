# Chapter 12 Data: Bayesian MTE Microsimulation

**Repository:** `higher-ed-policy-analysis-2nd-edition / data`
**Path:** [`data/ch12`](https://github.com/higher-ed-policy-analysis-2nd-edition/data/tree/main/ch12)
**Book:** *Higher Education Policy Analysis Using Quantitative Techniques*, Second Edition — Springer

---

## Overview

This directory contains the synthetic dataset used in **Chapter 12: Bayesian MTE Microsimulation — Cost–Benefit Analysis of a $100k Lifetime Cap on Grad PLUS Loans**. The dataset is a synthetic panel of 8,000 graduate students, calibrated to mirror the Baccalaureate and Beyond (B&B) longitudinal study population used elsewhere in the book, and designed to illustrate the marginal treatment effect (MTE) framework built up in Chapters 10–11, applied here to a Bayesian parametric-bootstrap cost–benefit analysis of a federal loan-cap policy.

The simulated policy context is a **$100,000 lifetime aggregate cap on federal Grad PLUS borrowing**, modeled after provisions of the *One Big Beautiful Bill* (Pub. L. 119-XX, effective July 1, 2026). Students whose cumulative Grad PLUS borrowing exceeds the cap face a binding credit constraint that may push them out of graduate enrollment. Chapter 12 uses the MTE framework to identify which students are displaced, estimate their counterfactual returns, and propagate coefficient uncertainty through 1,000 posterior draws to produce a full distribution of net social benefits, fiscal savings, and behavioral costs.

The data-generating process is independently constructed for this chapter — it is calibrated to the same B&B-style population design used in Chapters 10 and 11 (same demographic and instrument structure, same GA-funding instrument), but does not literally merge or extend any single prior chapter's dataset. It adds **Grad PLUS loan amounts** (`grad_plus_loans`) and **institutional revenue variables** (`annual_tuition`, `program_years`, `gross_tuition`, `net_inst_rev`) not present in earlier chapters, to support the institutional-impact arm of the CBA.

---

## Files in This Directory

| File | Description |
|------|-------------|
| `Example_12_1.dta` | Synthetic dataset — Stata format (compatible with Stata 19+; the canonical file loaded by both the Stata and R scripts) |

The companion analysis and generation scripts are located in the code repository:

| Script | Repository path |
|--------|-----------------|
| `Stata_code12.do` | [`code/ch12/Stata_code12.do`](https://github.com/higher-ed-policy-analysis-2nd-edition/code/blob/main/ch12/Stata_code12.do) |
| `R_code12.R` | [`code/ch12/R_code12.R`](https://github.com/higher-ed-policy-analysis-2nd-edition/code/blob/main/ch12/R_code12.R) |
| `Generate_Example_12_1.do` | [`code/ch12/Generate_Example_12_1.do`](https://github.com/higher-ed-policy-analysis-2nd-edition/code/blob/main/ch12/Generate_Example_12_1.do) — standalone script that produces this dataset |
| `Generate_Example_12_1.R` | [`code/ch12/Generate_Example_12_1.R`](https://github.com/higher-ed-policy-analysis-2nd-edition/code/blob/main/ch12/Generate_Example_12_1.R) — R version of the same |

`Stata_code12.do` and `R_code12.R` both download this file automatically on first run; they only generate a fresh synthetic dataset if the download fails. `Example_12_1.dta` is therefore the fixed, canonical population that both the chapter text and both analysis scripts reference.

---

## Dataset: `Example_12_1`

### Dimensions

| Property | Value |
|----------|-------|
| Observations | 8,000 |
| Variables | 27 |
| Unit of analysis | Individual graduate student (synthetic) |
| Random seed | `20251201` (Stata `set seed`; R `set.seed` — see note on RNG divergence below) |

### Variable Dictionary

#### Identifiers and Demographics

| Variable | Type | Description |
|----------|------|-------------|
| `id` | int | Student identifier (1–8,000) |
| `female` | binary | 1 = female (realized ≈ 51%) |
| `black` | binary | 1 = Black/African American (realized ≈ 13%) |
| `hispanic` | binary | 1 = Hispanic/Latino (realized ≈ 11%) |
| `asian` | binary | 1 = Asian (realized ≈ 7%) |
| `age_ba` | int | Age at bachelor's degree completion; bounded [21, 35]; mean ≈ 24.2 |
| `firstgen` | binary | 1 = first-generation college student (realized ≈ 26%) |
| `parent_income_q` | int | Parental income quartile (1 = lowest, 4 = highest) |
| `parent_grad` | binary | 1 = at least one parent holds a graduate degree (realized ≈ 39%) |
| `ugpa` | float | Undergraduate GPA, rounded to two decimals; bounded [2.0, 4.0]; mean ≈ 3.29 |

#### Graduate Program Area

Four mutually exclusive binary indicators. The omitted base category is **Other / Social Sciences**.

| Variable | Type | Realized Prevalence | Notes |
|----------|------|---------------------|-------|
| `stem_major` | binary | ≈ 21.6% | Science, Technology, Engineering, Mathematics |
| `bus_major` | binary | ≈ 18.1% | Business / MBA |
| `ed_major` | binary | ≈ 15.6% | Education |
| `health_major` | binary | ≈ 14.4% | Health and related fields |
| *(Other)* | — | ≈ 30.3% | Base category; no separate indicator |

#### Institutional and State Characteristics

| Variable | Type | Description |
|----------|------|-------------|
| `selective_inst` | binary | 1 = selective institution (realized ≈ 27%) |
| `public_ug` | binary | 1 = public undergraduate institution (realized ≈ 72%) |
| `state_unemp` | float | State unemployment rate at enrollment (%); bounded [2.5, 10.5]; mean ≈ 5.24 |
| `metro` | binary | 1 = metropolitan area (realized ≈ 68%) |

#### Instrument

| Variable | Type | Description |
|----------|------|-------------|
| `ga_funding_adj` | float | State graduate assistantship funding level ($000s, inflation-adjusted); bounded [2.0, 14.0]; mean ≈ 7.46. Serves as the excluded instrument in the probit selection model. Higher GA funding reduces loan dependence and raises completion propensity. |

#### Grad PLUS Loan Variables (Policy Exposure)

| Variable | Type | Description |
|----------|------|-------------|
| `grad_plus_loans` | float | Simulated lifetime Grad PLUS borrowing ($000s); bounded [0, 250]. Depends on program type, institutional selectivity, family income, GA funding, and an individual-specific unobserved borrowing propensity. |

Realized mean loan amounts by program area:

| Program area | Mean `grad_plus_loans` ($000s) |
|---|---|
| Business | 81.9 |
| STEM | 70.7 |
| Health | 66.5 |
| Education | 56.1 |
| Other | 47.4 |

Business/MBA programs carry the highest borrowing needs, consistent with the highest tuition schedule described below. The $100k policy cap binds for **≈ 9.6% of completers** in this synthetic sample (≈ 10.7% of the full sample, completers and non-completers combined).

#### Institutional Revenue Variables

| Variable | Type | Description |
|----------|------|-------------|
| `annual_tuition` | float | Annual graduate tuition ($000s); floor at $10k. Realized means: Business ≈ $61.0k/yr, Health ≈ $46.2k/yr, STEM ≈ $36.1k/yr, Other ≈ $26.3k/yr, Education ≈ $26.0k/yr. |
| `program_years` | float | Expected years to degree: 2.5 for STEM, 2.0 for all other programs. |
| `gross_tuition` | float | Gross tuition revenue per enrolled student = `annual_tuition` x `program_years` ($000s). |
| `net_inst_rev` | float | Net institutional revenue per student = 65% of `gross_tuition` ($000s). The 35% deduction represents variable instructional costs. An additional 20% of net revenue is assumed to cross-subsidize undergraduate aid and other programs, producing a multiplier effect on the institutional cost of cap-induced attrition. |

#### Outcome and Selection Variables

| Variable | Type | Description |
|----------|------|-------------|
| `masters` | binary | 1 = completed master's degree. Generated by a probit latent-index DGP with instrument `ga_funding_adj` and full covariate set. **Realized completion rate ≈ 37.6%** (3,011 of 8,000). |
| `ln_salary` | float | Log annual salary (observed outcome). Constructed from heterogeneous potential outcomes under a true cubic MTE polynomial: MTE(*u*) = −2.50 + 19.30*u* − 30.25*u*² + 15.12*u*³, where *u* = 1 − P̂(*D*=1|*Z*,*X*) is unobserved resistance to treatment. |
| `salary` | float | Annual salary in dollars = exp(`ln_salary`). |

---

## Data Generation Process

The dataset is fully synthetic. No real student records are used.

Key design choices:

- **Selection equation.** Completion propensity is driven by GA funding (instrument), GPA, parental background, first-generation status, and field. The realized completion rate is ≈ 37.6% — lower than a naive 50/50 split, reflecting the calibrated selection intercept.
- **Heterogeneous returns — negative selection on gains.** The true MTE polynomial has a single zero-crossing at *u* ≈ 0.17 and is **increasing** in *u*: MTE(0) ≈ −2.50, MTE(1) ≈ +1.67. Because *u* = 1 − P̂(*D*=1), this means students with the *highest* propensity to complete (low *u*, most likely to enroll and finish) have the *lowest* — even negative — returns, while students with the *lowest* propensity to complete (high *u*, least likely to enroll) have the *largest* returns. This is **negative** selection on gains: the students that financial and credit constraints currently keep out of graduate education are, on average, the ones with the most to gain from completing. This is what produces the chapter's ATU > ATT > ATE hierarchy and motivates the policy question — a binding loan cap disproportionately displaces exactly the students with above-average returns among completers.
- **Loan amounts.** Borrowing needs are tied to program-specific cost structures and family income, with individual-level noise. The $100k cap binds for roughly the top 10% of the loan distribution, concentrated among completers in higher-cost fields (Business, STEM, Health).
- **Institutional revenue.** Tuition schedules and cost-share parameters (65% net revenue rate, 20% cross-subsidy share) are calibrated to plausible ranges based on typical graduate program cost structures.
- **Random seed.** Both the Stata script (`set seed 20251201`) and the R script (`set.seed(20251201)`) use the same seed value, but Stata's and R's pseudo-random number generators are different algorithms — matching seeds do **not** produce matching draws across languages. `Example_12_1.dta`, generated by the Stata version, is the canonical file both analysis scripts load; the R generation script (`Generate_Example_12_1.R`) exists for cross-platform completeness, not as an independent source of truth for the canonical dataset.

---

## Figures Produced by the Analysis Scripts

| Figure | File (Stata) | File (R) | Description |
|--------|-------------|---------|-------------|
| Fig. 12.1 | `fig12_1_posterior_nb_Stata.png` | `fig12_1_posterior_nb_R.png` | Posterior distribution of net social benefits ($000s); 1,000 draws |
| Fig. 12.2 | `fig12_2_mte_policy_Stata.png` | `fig12_2_mte_policy_R.png` | MTE curve (posterior mean) with policy-affected region (*u* ∈ [0.30, 0.65]) shaded |
| Fig. 12.3 | `fig12_3_cba_decomp_Stata.png` | `fig12_3_cba_decomp_R.png` | CBA decomposition — fiscal savings, efficiency gain, behavioral cost |
| Fig. 12.4 | `fig12_4_param_posteriors_Stata.png` | `fig12_4_param_posteriors_R.png` | Posterior densities of ATE, ATT, and ATU (illustrates ATU > ATT > ATE) |
| Fig. 12.5 | `fig12_5_mte_byfield_Stata.png` | `fig12_5_mte_byfield_R.png` | MTE curves by graduate program area (Business, STEM, Health, Education, Other) |
| Fig. 12.6 | `fig12_6_inst_rev_byfield_Stata.png` | `fig12_6_inst_rev_byfield_R.png` | Net institutional revenue loss by program area |
| Fig. 12.7 | `fig12_7_full_cost_stack_Stata.png` | `fig12_7_full_cost_stack_R.png` | Full social cost stack — student and institutional channels combined |

All figures are exported in grayscale at approximately 1,200-pixel width, saved as `.png` files in `Output/graphs/`. The Stata scheme is `s2mono`; the R script uses a custom `theme_springer()` for the equivalent monochrome Springer print style.

---

## Software Requirements

### Stata

- **Version:** Stata 19 or later
- **User-written packages:** none required — all estimation uses built-in `probit`, `regress`, and `drawnorm`

### R

- **Version:** R 4.4 or later
- **Required packages:** `haven`, `MASS`, `sandwich`, `lmtest`, `car`, `dplyr`, `tidyr`, `ggplot2`, `scales`
- Install with: `install.packages(c("haven", "MASS", "sandwich", "lmtest", "car", "dplyr", "tidyr", "ggplot2", "scales"))`

---

## Cross-Chapter Dependencies

Chapter 12 builds directly on the causal inference toolkit developed in **Chapter 10** and the MTE/MPRTE estimation framework developed in **Chapter 11**:

- Chapter 10 data: [`data/ch10`](https://github.com/higher-ed-policy-analysis-2nd-edition/data/tree/main/ch10)
- Chapter 10 code: [`code/ch10`](https://github.com/higher-ed-policy-analysis-2nd-edition/code/tree/main/ch10)
- Chapter 11 data: [`data/ch11`](https://github.com/higher-ed-policy-analysis-2nd-edition/data/tree/main/ch11)
- Chapter 11 code: [`code/ch11`](https://github.com/higher-ed-policy-analysis-2nd-edition/code/tree/main/ch11)
- The field-specific ATE differentials used to construct Figure 12.5 are taken from **Table 10.1** of the Chapter 10 output (STEM: +0.134, Business: +0.885, Education: −0.165, Health: +0.038).

Chapter 12 estimates its own MTE polynomial from `Example_12_1.dta` directly — it does not import a fitted model, posterior, or prior from Chapter 10 or Chapter 11. The conceptual link is the shared framework (MTE, treatment-parameter hierarchy, GA-funding instrument), not a data or estimate handoff between chapters.

---

## Citation

> Titus, M. A. (forthcoming). *Higher Education Policy Analysis Using Quantitative Techniques* (2nd ed.). Springer.

Code development was assisted by Claude (Anthropic, 2025–2026). The author provided all methodological specifications and reviewed, tested, and validated all code and output.

---

## License

Data are synthetic and released for educational use in connection with the book. See the repository root [`LICENSE`](https://github.com/higher-ed-policy-analysis-2nd-edition/data/blob/main/LICENSE) for terms.
