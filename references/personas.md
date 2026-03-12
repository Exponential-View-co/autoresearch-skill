# Personas Reference

How to select and use personas from the ev-personas repository in autoresearch loops.

---

## Repository access

**Repo:** `Exponential-View-co/ev-personas` (private)
**Token:** stored in MEMORY.md as `ev-personas read token` (key: `EV_PERSONAS_TOKEN`)
- Current value: stored in MEMORY.md (search: "ev-personas read token") — **never commit this value**

**Base API URL:** `https://api.github.com/repos/Exponential-View-co/ev-personas/contents/`

---

## Repository structure

```
personas/
  synthetic/      ← General-purpose synthetic personas (most useful for autoresearch)
  segments/       ← Market segment personas
  technical/      ← Technical/specialist personas
  book-editors/   ← Book editorial personas
panels/           ← Pre-built multi-persona panels for common use cases
registry.json     ← Full index of all personas with tags and domains
```

---

## Available synthetic personas (as of 260312)

| ID | Name | Domain | Best for |
|---|---|---|---|
| carmen-li | Carmen Li | benchmark, strategy, financial data | competitive analysis, strategy stress-test |
| chip-analyst | Chip Analyst | semiconductors, compute | technical product, hardware |
| claire-fontaine | Claire Fontaine | — | — |
| ev-platform-strategist | EV Platform Strategist | platform, strategy | product strategy, platform thinking |
| incumbent-adversary | Incumbent Adversary | competitive threat | positioning, competitive stress-test |
| james-harrington | James Harrington | — | — |
| ken-cukier | Ken Cukier | tech journalism, media | writing, book positioning |
| kenji-nakamura | Kenji Nakamura | — | — |
| lance-uggla | Lance Uggla | data, financial markets | data product strategy |
| marcus-webb | Marcus Webb | — | — |
| michael-zhao | Michael Zhao | — | — |
| priya-krishnamurthy | Priya Krishnamurthy | — | — |
| ravi-mehta | Ravi Mehta | — | — |
| sarah-chen | Sarah Chen | — | — |
| suki-chen | Suki Chen | — | — |
| synthetic-quant-buyer | Synthetic Quant Buyer | quant, finance | financial product |
| vc-stress-tester | VC Stress-Tester | venture capital | pitch, startup strategy |

---

## Available pre-built panels

| Panel | Best for |
|---|---|
| `book-editors.yaml` | Book content, titles, positioning |
| `competitive-threat.yaml` | Competitive analysis |
| `ev-monetisation.yaml` | Monetisation strategy |
| `full-panel.yaml` | General strategy review |
| `gtm-pricing.yaml` | Go-to-market, pricing |
| `institutional-credibility.yaml` | Academic / institutional work |
| `metis-full-review.yaml` | Metis-specific review |
| `strategy-stress-test.yaml` | Strategy stress-testing |

---

## How to fetch a persona

```bash
EV_PERSONAS_TOKEN="<from MEMORY.md>"

# List personas
curl -s -H "Authorization: Bearer $EV_PERSONAS_TOKEN" \
  "https://api.github.com/repos/Exponential-View-co/ev-personas/contents/personas/synthetic" | jq -r '.[].name'

# Fetch a specific persona
curl -s -H "Authorization: Bearer $EV_PERSONAS_TOKEN" \
  "https://api.github.com/repos/Exponential-View-co/ev-personas/contents/personas/synthetic/ken-cukier.yaml" | jq -r '.content' | base64 -d
```

---

## Selection logic for autoresearch

When selecting personas for a loop's evaluator panel:

1. **Identify the problem domain** (writing, strategy, pitch, research, etc.)
2. **Match to a pre-built panel** if one fits — use as-is or select 2–3 from it
3. **If no panel fits**: pick from synthetic personas matching domain tags, OR create ad-hoc (see below)
4. **Always include a skeptic** for strategy/positioning loops — `incumbent-adversary` or `vc-stress-tester`
5. **For writing loops**: use `ken-cukier` (media/cultural) + one policy reader + one tech reader

---

## Creating ad-hoc personas

If ev-personas has nothing close, create a persona inline in `evaluate.md`. Required fields:

```
## Persona: [Name or archetype]
- Role: [Senior journalist / Policy director / Tech executive / etc.]
- Context: [What are they doing when they encounter this? What pain are they carrying?]
- Reading for: [What do they want to get from this?]
- Penalise: [What will lose their trust?]
```

Ad-hoc personas are defined once at the top of `evaluate.md` and stay fixed for the run.

---

## Note on IGB loops

The existing IGB autoresearch loops use ad-hoc personas (The Intelligent Generalist at the Frame-Break Moment — policy / tech / media variants). These were created before ev-personas was available. They remain valid and should not be replaced mid-run.
