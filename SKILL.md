---
name: autoresearch
description: Autonomous iterative improvement loop for any writing or thinking task — thesis frames, book titles, essay angles, product positioning, strategy arguments. Give a problem statement, the agent auto-designs the oracle (scoring dimensions + judge panel), shows it for approval, then iterates one change at a time until convergence. Based on Karpathy's autoresearch pattern applied to ideas. Trigger phrases: "/autoresearch", "run an autoresearch loop on", "iterate on this using autoresearch", "use the autoresearch skill". Existing named loops: igb-thesis, igb-title. Discord channel: #autoresearch (1480667040174833878).
---

# Autoresearch Skill

Autonomous improvement loop for writing and thinking tasks. Human provides the problem. Agent designs the oracle, iterates, evaluates, keeps or discards, repeats until convergence.

Based on Andrej Karpathy's autoresearch pattern (2026-03-07): the insight is that any domain with a scalar oracle and fast feedback can be optimised. The oracle quality determines everything.

---

## Invocation

```
/autoresearch "problem statement" [--oracle-hint "what matters"] [--iterations N] [--auto]
```

**Examples:**
```
/autoresearch "sharpen the positioning for Orbit's enterprise pitch"
/autoresearch "iterate on this Substack intro" --oracle-hint "boldness and hook matter most"
/autoresearch "find the best frame for my WEF paper abstract" --iterations 15
/autoresearch igb-thesis          ← resume named loop
/autoresearch igb-title           ← resume named loop
```

**Flags:**
- `--oracle-hint "X"` — incorporate X as a named dimension or evaluator constraint
- `--iterations N` — hard cap (default: run to convergence, max 30)
- `--auto` — skip oracle approval, run immediately with auto-designed oracle

**Default:** always show oracle for approval before looping.

---

## Phase 1 — Setup (auto-designed)

When invoked with a new problem:

### 1. Parse the problem
Determine:
- **What is being iterated** (thesis frame, title, argument, abstract, positioning line, etc.)
- **Task slug** for directory naming (e.g. `orbit-pitch`, `wef-abstract`)
- **Problem type** (see `references/oracle-design.md` for patterns by type)

### 2. Design the oracle
Read `references/oracle-design.md`. Select 3–4 scoring dimensions appropriate to the problem type. If `--oracle-hint` was passed, incorporate as a named dimension. Produce a draft `evaluate.md` from `templates/evaluate.md.tmpl`.

**Oracle must have:**
- 3–4 named, non-redundant dimensions
- Each dimension: vivid label + clear question + "what a 10 looks like"
- Scoring: geometric mean per persona, arithmetic mean across personas
- Output format: score per dimension per persona, FINAL SCORE, KEEP/DISCARD, WEAKEST ELEMENT, SUGGESTED DIRECTION

### 3. Select the panel
Read `references/personas.md`. Match to problem domain:
1. Check pre-built panels in ev-personas — use if fit
2. Select 2–3 synthetic personas from ev-personas matching domain tags
3. If nothing fits: create ad-hoc personas inline in evaluate.md

Default panel size: 3. Add a skeptic (incumbent-adversary or vc-stress-tester) for strategy/pitch loops.

### 4. Generate experiment_v0.md
From `templates/experiment_v0.md.tmpl`. Create starting state from the problem statement. If the user pasted draft content, use it as v0.

### 5. Show oracle for approval
Present the following for confirmation (unless `--auto`):
```
ORACLE DESIGN — [task slug]
Problem: [restate]
Experiment: [what will be iterated]
Loop dir: ~/clawd/[task-slug]-autoresearch/

PANEL: [persona 1] / [persona 2] / [persona 3]

DIMENSIONS:
  D1 — [Name]: [Question]
  D2 — [Name]: [Question]
  D3 — [Name]: [Question]
  [D4 — Name: Question]

SCORING: geometric mean per persona → arithmetic mean across panel

Hard constraints: [list]
Direction of travel: [bolder/sharper/more specific/etc.]

Reply ✅ to start, or give corrections.
```

Do NOT proceed to Phase 2 until confirmed (or `--auto` was passed).

### 6. Create loop directory and files
```bash
mkdir -p ~/clawd/[task-slug]-autoresearch/
# Write program.md, evaluate.md, [experiment]_v0.md
```

---

## Phase 2 — The Loop

### Iteration agent (sonnet-bedrock)
Spawn with: `program.md` contents + current best experiment file. Instruction: generate ONE iteration — change ONE element per the priority order. Output the full new version.

### Evaluator agent (opus-bedrock — always strong)
Spawn with: `evaluate.md` contents + new experiment file + "previous best score was X.XX". Returns: FINAL SCORE, KEEP/DISCARD, WEAKEST ELEMENT, SUGGESTED DIRECTION.

### Route the result
- **KEEP:** save as new `[experiment]_vN.md` (increment N), update current best score, append to run log
- **DISCARD:** log reason and suggested direction, return to iteration agent with direction hint

### Convergence signals
- 10 consecutive DISCARD iterations → loop has converged, report to Azeem
- Iteration budget exhausted (`--iterations N`)
- Score delta < 0.05 for 5+ iterations → near-convergence, flag to Azeem

### Batch updates to Discord
After every 5 iterations, post to #autoresearch (`1480667040174833878`):
```
[Task] Batch update: iterations 1–5
Best score: X.XX (vN)
Kept: N | Discarded: N
Weakest element: [X]
Next direction: [Y]
```

---

## Phase 3 — Convergence Report

When convergence is reached:
```
/autoresearch [task] CONVERGED after [N] iterations

Best candidate: [experiment]_vN.md
Final score: X.XX
Score history: [v0: X.XX → vN: X.XX]
Winning changes: [list of kept changes]
What didn't move the score: [list of discarded directions]

Files: ~/clawd/[task-slug]-autoresearch/
Run log: run-log-[date].md

Recommend: review [experiment]_vN.md and decide if score is sufficient or restart with modified oracle.
```

---

## Named Loops (existing instances)

These are running instances of the pattern — do not delete or modify their program.md/evaluate.md.

| Name | Directory | Status | Best score |
|---|---|---|---|
| `igb-thesis` | `~/clawd/igb-autoresearch/` | Active | ~8.5+ (v18) |
| `igb-title` | `~/clawd/igb-title-autoresearch/` | Converged | 9.66 — "The Cage and the Curve" |

To resume: `/autoresearch igb-thesis` reads the existing program.md, evaluate.md, and latest thesis_frame_vN.md, then continues the loop.

---

## Run log format

Append to `run-log-[YYMMDD].md` after each iteration:

```
## [YYMMDD HH:MM] Iteration [N]
Changed: [what element was changed]
Score: [new] vs [previous best]
Decision: KEEP / DISCARD
Reason: [1 sentence]
Next direction: [1 sentence]
```

---

## Key rules (never violate)

- **Change ONE element per iteration** — the loop only works if changes are atomic
- **The oracle is frozen** for the duration of a run — never modify evaluate.md mid-run
- **Evaluator must be strong** — always opus-bedrock for evaluation
- **Direction: always bolder** — discard changes that hedge or qualify the claim
- **Log everything** — run logs in the loop directory; Discord gets summaries
- **Oracle quality = convergence quality** — spend time on oracle design; a vague oracle produces vague search

---

## Reference files

- **`references/oracle-design.md`** — oracle patterns by problem type (writing, strategy, title, essay, abstract) + dimension selection guide + panel size guidance
- **`references/personas.md`** — ev-personas repo access, available personas + panels, selection logic, ad-hoc creation guide
- **`templates/program.md.tmpl`** — parameterised program template
- **`templates/evaluate.md.tmpl`** — parameterised evaluate template
- **`templates/experiment_v0.md.tmpl`** — starting state template
