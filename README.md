# artemis-fit-formatter
tidy Artemis output for publication

A tiny helper script that takes
  (1) the parameter block (Table 1) written by Artemis (doi:10.1107/S0909049505012719) after an EXAFS fit
  (2) the path list (Table 2) printed underneath,
matches every scattering path to its corresponding ΔR / σ² errors, rounds the numbers with consistent significant‑figure logic, and emits one‑line summaries ready to paste into a manuscript or supporting information.

Why?

Artemis already reports fit parameters, but:
    + ΔR errors are listed in one table, σ² errors in another, forcing tedious copy‑paste
    + paths that belong to the same coordination shell appear on separate rows.

This script automates the bookkeeping and presents each shell on a single, human‑readable line.

### Input format

Straight from the Artemis log – no editing required.

Table 1 (fit parameters):

```json
{{
  enot    =  -2.98  +/-  2.50   (0.0000)
  delr    =  -0.006 +/-  0.067  (0.0000)
  ss      =   0.010 +/-  0.011  (0.0030)
  delr2   =  -0.085 +/-  0.015  (0.0000)
  ss2     =   0.009 +/-  0.002  (0.0030)
}}
```

Table 2 (path list – truncated example):

```json
{{
path                      degen amp    sigma^2  e0  reff  delta_R  R
---------------------------------------------------------------------
"FEFF0: Path 1: [B1_1]"     1   0.956  0.01009  ...              1.97665
"FEFF0: Path 4: [Ni1_1]"    1   0.956  0.00946  ... -0.08508     2.31092
"FEFF0: Path 5: [Ni1_2]"    2   0.956  0.00946  ... -0.08508     2.39232
...
}}
```

Output example

Running the demo embedded in main() prints:

```
Ordered grouped results:
"FEFF0: Path 1: [B1_1]" | R = 1.98 (0.07) | sigma² = 1.01 (1.19)*10²
R = 2.31 - 2.49 (0.01) | sigma² = 0.95 (0.17)*10²
```

### Key features
| Feature                      | How it works                                                                                                                                          |
| ---------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Cascade rounding**         | `cascade_round()` ensures each value is rounded stepwise (5 → 4 → 3 → 2 decimals) so that the displayed precision is compatible with its uncertainty. |
| **Automatic error matching** | `match_parameters()` pairs a `delta_R` in Table 2 with its matching `delr` (and `ss`) entry in Table 1 to fetch the correct ± error.                  |
| **Path grouping**            | Paths that share the same `delrN / ssN` pair are merged; the script outputs an R–range when multiple rows belong to one coordination shell.           |
| **Minimal dependencies**     | Pure Python ≥ 3.7; no external libraries except the standard `re` and `decimal` modules.                                                              |
