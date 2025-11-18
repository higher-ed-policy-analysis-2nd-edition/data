# Chapter 8 — Example data files

These are the reformatted example data files used by Chapter 8 of
"Higher Education Policy Analysis Using Quantitative Techniques (2nd Edition)"
by Marvin A. Titus.

Important note: the numbers embedded in the file names correspond to the book
sections that use each dataset. For example, `Example_8_2.dta` contains the
data used in Section 8.2 of the book; `Example_8_8_2.dta` is used in Section
8.8.2, and so on. This naming convention makes it simple to find the dataset
referenced by a specific section or example in the chapter text or the
chapter's code files.

---

## Contents

- `Example_8_2.dta` — Stata dataset for Section 8.2 (small example)
- `Example_8_6.dta` — Stata dataset for Section 8.6 (larger example)
- `Example_8_8_2.dta` — Stata dataset for Section 8.8.2 (extended example)

(Timestamps and file sizes are preserved in the repository metadata — see GitHub UI for details.)

---

## Purpose & provenance

- These datasets are the cleaned, reformatted example files used by the
  Chapter 8 code and exercises (see `code/ch8`).
- Data are either derived from public sources discussed in the chapter
  (NCES, IPEDS, or other public collections) or are synthetic examples
  prepared for pedagogical demonstration. Chapter 8 and the accompanying
  code identify the original sources where needed.

---

## How to use these files

### In Stata
The chapter's do-files expect these files to be available in `data/ch8` or in
the working directory. Example:
```stata
use "data/ch8/Example_8_6.dta", clear
describe
list in 1/20
```
If you run the chapter do-files from `code/ch8`, they will typically reference
these files with relative paths (e.g., `../data/ch8/Example_8_6.dta`) — check
the top of the do-file for the working-directory instructions.

### In R
Use the `haven` package to read Stata `.dta` files:
```r
install.packages("haven")     # if not already installed
library(haven)
df <- read_dta("data/ch8/Example_8_6.dta")
str(df)
head(df)
```

---

## Verification & best practices

- The file names are intentionally mapped to book sections. When you follow an
  example in Section 8.x, open the correspondingly named file to inspect the
  exact variables used in that example.
- Before running analysis, inspect variable labels and a few rows to confirm
  the expected layout:
  - Stata: `describe`, `codebook varname`, `list in 1/10`
  - R: `str(df)`, `labelled::var_label(df)` (if using labelled data), `head(df)`
- If a code example does not find a dataset, confirm your working directory or
  adjust the path in the code to point at `data/ch8/`.

---

## Reporting issues & contributions

- If you find a problem with a dataset (missing labels, encoding issues,
  or obvious data errors), please open an issue in the code repository:
  https://github.com/higher-ed-policy-analysis-2nd-edition/code/issues
  Include the filename and a brief description of the problem.
- Pull requests that improve dataset metadata (add data dictionaries,
  variable descriptions, or small fixes) are welcome. Please document any
  changes and include a short note in the PR describing why the change is needed.

---

## Citation & license

- Please cite the book when using these data files in research or teaching:
  Titus, M. A. (2025). *Higher Education Policy Analysis Using Quantitative Techniques* (2nd ed.). Springer.
- If the dataset originates from an external public source (e.g., NCES, IPEDS),
  cite the original data provider as required.
- See the repository top-level LICENSE for permitted use and redistribution.

---

Last updated: 2025-11-17