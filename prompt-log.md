---
type: prompt-log
owner: cheungming-chengleever
started: 2026-10-02
---

# Prompt log

| Date | Tool | What I asked | What I got | What I did with it |
|---|---|---|---|---|
| 2026-10-01 | Claude (web) | asked for a critique of my brief without rewriting  | got unstated assumptions and unsupported claims  | added the missing sections
| 2026-11-05 | Claude (web) | asked for example sections (Purpose, Inputs, Structure, Calculation logic, Conventions, Validation rules, Outputs) using a different business | got a made-up bakery example for each section | wrote my own farm versions using the format |
| 2026-11-05 | Claude (web) | asked for critique of each spec section as I drafted it, without rewriting | got leftover bakery values/words, name mismatches between sections, rounded values that should be derivations, an undefined decision-cell name, and an ambiguous MC crossing definition | fixed each in my spec; chose "largest n where MC ≤ price" for the crossing |
| 2026-11-05 | Claude (web) | asked for a final review of the full spec | got a few small consistency fixes; Claude also said its own calculation at 10/20/30 using my formulas comes to about $42,762 | made the fixes; will confirm the profit in my own audit, not rely on Claude's number |
| 2026-11-05 | Claude (code) | gave Claude my committed spec.md as-is and asked: "Build model.xlsx exactly as specified in this file. Use the names in the spec as Excel named ranges. Every calculated cell must be a formula. Do not change or add to the spec. If something in the spec is unclear, ask me instead of guessing." | got model.xlsx with [five sheets: Inputs, Costs, MC_Schedule, Optimize, Checks] | opened in desktop Excel and checked the names and formulas |
| 2026-11-05 | Claude (web) | gave Claude my committed spec as-is and asked it to build model.xlsx | got a 5-sheet workbook with named ranges; but decision cells were pre-filled with 10/20/30, the file had no saved values, and the crossing formula didn't follow my spec | ran Solver myself from 0/0/0 and 20/0/0; fixed the crossing definition in the spec |
| 2026-11-05 | Claude (web) | asked for help when Mac Solver turned constraints into BEDS_TOM <= BEDS_TOM | explained Mac Solver can't reference other sheets | updated spec Structure #4; Claude Code added linked limit cells; re-ran Solver |

## Errors Caught
   - 2026-11-05 — Claude suggested "largest n where MC ≤ price" as the crossing
     definition. It fails for carrots and mesclun because MC dips below price again
     after farmer hours run out. Corrected the spec.
- 2026-11-05 — Claude (web) suggested "largest n where MC ≤ price" as the
  crossing definition. It fails for carrots and mesclun because MC drops
  below price again after farmer hours run out. Caught in the audit; corrected
  the spec.
- 2026-11-05 — Claude Code pre-filled the decision cells with the published
  answer (10/20/30) and saved the workbook in the wrong repo. Caught before
  running Solver; fixed.
