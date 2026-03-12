# autoresearch-skill

An OpenClaw skill implementing Karpathy's autoresearch pattern applied to ideas — autonomous iterative improvement for writing, thinking, and strategy tasks.

## What it does

Give the agent a problem. It designs a scalar oracle (scoring dimensions + judge panel), shows it for approval, then iterates one change at a time until convergence. Each iteration: generate variant → evaluate → keep if better → discard if not → repeat.

## Invocation

```
/autoresearch "problem statement" [--oracle-hint "what matters"] [--iterations N] [--auto]
```

**Examples:**
```
/autoresearch "sharpen the positioning for our enterprise pitch"
/autoresearch "iterate on this Substack intro" --oracle-hint "boldness and hook matter most"
/autoresearch "find the best frame for our paper abstract" --iterations 15
```

## How it works

1. **Phase 1 — Setup:** Agent designs oracle (3–4 scoring dimensions), selects judge panel from ev-personas, generates starting state, shows oracle for approval
2. **Phase 2 — Loop:** Iteration agent (sonnet) generates one change → evaluator agent (opus) scores → keep or discard → repeat
3. **Phase 3 — Convergence:** Report best candidate, score history, winning/discarded directions

## Key design decisions

- **Oracle always shown first** before looping (bypass with `--auto`)
- **One change per iteration** — atomic changes only, loop property depends on this
- **Evaluator model: opus** — weak evaluators produce noisy scores
- **Evaluator frozen** — never modify `evaluate.md` mid-run; start a new run if oracle needs changing
- **Direction: always bolder** — changes that hedge or qualify are discarded regardless of score

## Structure

```
SKILL.md                          ← Main skill instructions
references/
  oracle-design.md                ← Oracle patterns by problem type
  personas.md                     ← ev-personas access + selection guide
templates/
  program.md.tmpl                 ← Iteration agent instructions template
  evaluate.md.tmpl                ← Evaluator template
  experiment_v0.md.tmpl           ← Starting state template
```

## Origin

Based on Andrej Karpathy's autoresearch pattern (released 2026-03-07). The insight: any domain with a scalar oracle and fast feedback can be optimised. Originally developed for iterating book thesis frames and titles for Azeem Azhar's *IGB* book.

## Install

Copy `~/.openclaw/skills/autoresearch/` or install the `.skill` file via OpenClaw skill manager.
