---
type: spec
capability: marginal-analysis
engagement: perfect-competition
date: 2026-11-05
status: draft
built_with: ""
---

# Marginal analysis — model specification

## Purpose
This model supports a farmer's decision about how many beds of tomatoes, carrots, and mesclun to plant each 36-week season, given limited labor hours, a total bed cap at 64, tomato bed cap at 20, carrot bed cap at 20, mesclun bed cap at 30, and labor cap at 4 temp workers. It must report the profit-maximizing crop mix and, for each crop, the bed count at which the cost of one more bed equals what it sells for per bed per season, as well as the profit at the best mix. 

## Inputs — the named contract
| Name | Value | Unit | Source |
|---|---|---|---|
| TOM_CAP | 20 | beds/season | Case scenario, crop table |
| TOM_PRICE | 8800 | revenue USD/bed | Case scenario, crop table |
| TOM_FIELD_HRS | 2.5 | field hrs/wk/bed | Case scenario, crop table |
| TOM_FERT | 880 | fertilizer USD/bed | Case scenario, crop table |
| TOM_DIM_RATE | .1 (decimal; = 10%) | diminishing-returns rate | Case scenario, crop table |
| CAR_CAP | 20 | beds/season | Case scenario, crop table |
| CAR_PRICE | 2094 | revenue USD/bed | Case scenario, crop table |
| CAR_FIELD_HRS | = TOM_FIELD_HRS ÷ 3 | field hrs/wk/bed | Derived: carrots need one-third of tomato field hrs (case shows 0.833 = 2.5 ÷ 3) |
| CAR_FERT | 440 | fertilizer USD/bed | Case scenario, crop table |
| CAR_DIM_RATE | .025 (decimal; = 2.5%) | diminishing-returns rate | Case scenario, crop table |
| MES_CAP | 30 | beds/season | Case scenario, crop table |
| MES_PRICE | 2700 | revenue USD/bed | Case scenario, crop table |
| MES_FIELD_HRS | = TOM_FIELD_HRS ÷ 2 | field hrs/wk/bed | Derived: mesclun needs half of tomato field hrs (case shows 1.25 = 2.5 ÷ 2) |
| MES_FERT | 880 | fertilizer USD/bed | Case scenario, crop table |
| MES_DIM_RATE | .0125 (decimal; = 1.25%) | diminishing-returns rate | Case scenario, crop table |
| WEEKS | 36 | weeks per season | Case scenario, farm table |
| FIXED_COSTS | 20000 | USD per season | Case scenario, farm table |
| BEDS_AVAIL | 64 | beds per season (all crops) | Case scenario, farm table |
| FARMER_PAY | 50000 | USD per season | Case scenario, farm table |
| FARMER_PAID_HRS_WEEK | 40 | paid hrs per week | Case scenario, farm table |
| FARMER_PAID_HRS | = FARMER_PAID_HRS_WEEK × WEEKS | paid hrs per season | Derived: farmer paid hrs per week x WEEKS |
| FARMER_FIELD_SHARE | 0.5 (decimal; = 50%) | share of paid hrs at the field | Case scenario, farm table |
| FARMER_FIELD_HRS | = FARMER_PAID_HRS × FARMER_FIELD_SHARE | field hrs per season | Derived: paid hrs × field share |
| FARMER_RATE | = FARMER_PAY ÷ FARMER_PAID_HRS | USD per hr | Derived: pay ÷ paid hrs |
| TEMP_MAX | 4 | workers | Case scenario, farm table |
| TEMP_PAY | 25000 | USD per TEMP per season | Case scenario, farm table |
| TEMP_HRS_WEEK | 40 | paid hrs per week per temp | Case scenario, farm table |
| TEMP_HRS_EACH | = TEMP_HRS_WEEK × WEEKS | field hrs per temp per season | Derived: temp hrs per week × WEEKS |
| TEMP_RATE | = TEMP_PAY ÷ TEMP_HRS_EACH | USD per hr | Derived: TEMP pay ÷ TEMP hrs |
## Structure
The workbook has five sheets:

1. **Inputs** — every value from the Inputs table above, each as a named
   range. No calculations here except the derived inputs.
2. **Costs** — for the current bed mix from the decision cells on Optimize: crop labor hours, total labor
   hours, farmer hours used, temp workers hours used, temp workers count, total labor
   dollars, blended labor rate, each crop's labor cost at that rate, revenue, fertilizer, fixed costs, and profit.
3. **MC_Schedule** — one table per crop, each crop on its own,
   from 1 bed up to its cap: field hours at each bed count, total cost
   at each bed count, standalone temp count and over-cap flag, marginal cost of each added bed, and revenue per
   bed in USD for comparison.
4. **Optimize** — the three decision cells (beds of tomatoes, carrots,
   mesclun) that Solver changes, the profit cell it maximizes, and one
   check cell per constraint showing TRUE/FALSE.
5. **Checks** — each [Cell] validation rule as a TRUE/FALSE cell using
   ABS(model − target) <= tolerance, plus a summary cell that is TRUE only
   if every check passes.

## Calculation logic
All formulas use the names from the Inputs table. C stands for each
crop (TOM, CAR, MES); n is the number of beds of that crop; Σ means the sum over TOM, CAR, and MES.

**Decision cells**
(these are the cells Solver changes and that they’re whole numbers, 0 or more)
BEDS_TOM = beds of tomatoes planted
BEDS_CAR = beds of carrots planted
BEDS_MES = beds of mesclun planted

**Field hours for n beds of one crop**
FIELD_HRS_C(n) = n × C_FIELD_HRS × WEEKS × (1 + C_DIM_RATE)^n

**Totals for the current mix** (n = BEDS_C)
TOTAL_HRS = FIELD_HRS_TOM + FIELD_HRS_CAR + FIELD_HRS_MES

**Who works the hours — farmer first, then temp workers**
FARMER_FIELD_HRS_USED  = MIN(TOTAL_HRS, FARMER_FIELD_HRS)
TEMP_HRS_USED = MAX(0, TOTAL_HRS − FARMER_FIELD_HRS)
TEMP_COUNT    = TEMP_HRS_USED ÷ TEMP_HRS_EACH

**Labor dollars and blended rate**
LABOR_COST   = FARMER_FIELD_HRS_USED × FARMER_RATE + TEMP_HRS_USED × TEMP_RATE
BLENDED_RATE = LABOR_COST ÷ TOTAL_HRS   (0 if TOTAL_HRS = 0)

**Per CROP**
LABOR_C   = FIELD_HRS_C(BEDS_C) × BLENDED_RATE
REVENUE_C = BEDS_C × C_PRICE
FERT_C   = BEDS_C × C_FERT

**Total beds**
TOTAL_BEDS = BEDS_TOM + BEDS_CAR + BEDS_MES

**Profit**
PROFIT = Σ REVENUE_C − Σ LABOR_C − Σ FERT_C − FIXED_COSTS

**Marginal cost schedule** (each CROP on its own, n = 1 to C_CAP)
COST_C(n) = labor cost (farmer hours first, then temp) plus fertilizer cost, for n beds of that crop alone
MC_C(n)   = COST_C(n) − COST_C(n − 1), with COST_C(0) = 0

## Conventions
1. **Farmer first.** The farmer's field hours (FARMER_FIELD_HRS) are used
   before any temp hours. Temps cover only the hours left over.
2. **Hours-used costing.** Labor is charged for the hours actually
   worked, at FARMER_RATE or TEMP_RATE. A temp is never charged as a
   full season's salary if only part of their hours are used.
3. **Blended rate for crops.** Each crop's labor cost is its FIELD_HRS_C × BLENDED_RATE. The farmer-vs-temp split is a farm-wide fact,
   not assigned to any one crop.
4. **Temp count is not rounded.** TEMP_COUNT may be a fraction
   (e.g., 1.4). It is compared to TEMP_MAX as-is.
5. **Farmer's pay.** The farmer's pay enters profit only through
   FARMER_FIELD_HRS_USED × FARMER_RATE. Farmer hours not used in production
   are not charged to profit.
6. **Zero beds.** If a crop has 0 beds, its hours, revenue,
   and costs are 0. If total hours are 0, BLENDED_RATE is 0.
7. **No rounding in calculations.** Values are rounded only for display.
8. **Solver settings.** Maximize PROFIT by changing the three decision
   cells (BEDS_TOM, BEDS_CAR, BEDS_MES), GRG Nonlinear, subject to:
   - BEDS_C <= C_CAP for each crop
   - TOTAL_BEDS <= BEDS_AVAIL
   - TEMP_COUNT <= TEMP_MAX
   - BEDS_C >= 0
   - BEDS_C = integer

   Options: Integer Optimality set to 0% (not Excel's 1% default), and
   "Ignore Integer Constraints" left unchecked.

   Runs: Solver is run twice, once starting from 0/0/0 and once from
   20/0/0 (BEDS_TOM/BEDS_CAR/BEDS_MES). The result of each run is
   recorded in Audit findings. If they disagree, the higher-profit
   result is kept.

   Saving: choose "Keep Solver Solution" after each run, save the Solver
   model to an empty range on the Optimize sheet (Load/Save → Save), and
   save the workbook last from desktop Excel.
9. **Standalone schedule past the temp cap.** The MC_Schedule tables
   ignore TEMP_MAX: if a crop alone needs more hours than FARMER_FIELD_HRS
   plus TEMP_MAX × TEMP_HRS_EACH, the extra hours are still charged at
   TEMP_RATE. Each row shows the standalone temp count and a flag
   (TRUE/FALSE) marking rows where it exceeds TEMP_MAX. Those rows show
   the cost curve's shape only; they are not feasible plans.

## Validation rules
Rules marked [Cell] are built as TRUE/FALSE cells on the Checks sheet
using ABS(model − target) <= tolerance; the overall pass cell is TRUE
only if every [Cell] rule is TRUE. Rules marked [Manual] are checked by
hand after the build and recorded in Audit findings.

**Check figures (from the case)**
1. [Cell] Optimal mix: BEDS_TOM = 10, BEDS_CAR = 20, BEDS_MES = 30,
   tolerance 0 beds for each (decision cells are integers, so each must
   match exactly).
2. [Cell] Season profit at the optimal mix: $42,762, tolerance ±$5.
3. [Cell] Standalone P ≈ MC crossing: tomato ~10, carrot ~10, mesclun ~6
   beds, tolerance ±1 bed.
   
**Hand calculation**
4. [Cell] One bed of tomatoes alone: FIELD_HRS_TOM(1) = 1 × 2.5 × 36 × 1.10
   = 99 hours, tolerance ±0.01 hours. Read from the field hours column
   of the tomato table on MC_Schedule, row n = 1.

**Constraints**
5. [Cell] Every constraint in Conventions #8 holds at the solution.

**Structural rules**
6. [Manual] No error cells (#REF!, #DIV/0!, #NAME?, #VALUE!) anywhere in the workbook.
7. [Manual] Every calculated cell contains a formula; only Inputs values and the
   decision cells are typed numbers.
8. [Manual] Changing any input changes the outputs (no pasted values).

## Outputs
The model reports, on the Optimize sheet:
1. Optimal mix: BEDS_TOM, BEDS_CAR, BEDS_MES
2. PROFIT at the optimal mix
3. TOTAL_BEDS and TEMP_COUNT at the optimal mix
4. Per crop at the optimal mix: REVENUE_C, LABOR_C, FERT_C
5. BLENDED_RATE at the optimal mix

On the MC_Schedule sheet:
6. MC_C(n) for each crop from 1 to C_CAP, shown next to C_PRICE
7. Standalone crossing point for each crop: the largest n
   where MC_C(n) <= C_PRICE

On the Checks sheet:
8. TRUE/FALSE for every validation rule, plus an overall pass cell

## Audit findings
