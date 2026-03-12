# Oracle Design Patterns

Reference for designing scoring dimensions from a problem type. Load when designing `evaluate.md` for a new autoresearch loop.

---

## Core principle

A good oracle has:
- **3–4 dimensions** (not more — geometric mean punishes zeros)
- **Each dimension measurable** by a lay reader simulation, not expert knowledge
- **Non-redundant** — dimensions should be orthogonal, not rewordings of each other
- **Named with a vivid label** (e.g. "Shelf-stop", not "Attractiveness")

Always use **geometric mean** per persona, then **arithmetic mean** across personas. This penalises fatal weaknesses (a dimension scoring 1 tanks the whole score) without letting one 10 mask three 5s.

---

## Patterns by problem type

### Writing — Thesis / Frame / Argument

Best for: book theses, essay frames, intellectual arguments, paradigm claims.

| Dimension | Question | What a 10 looks like |
|---|---|---|
| **Frame visibility** | Does this make the hidden assumption suddenly visible and nameable? | Reader thinks "I can't unsee this now" |
| **Relief** | Does the new frame feel like resolution, not just replacement? | Reader feels confusion dissolved, not swapped |
| **Portability** | Can the reader apply the frame to a new scenario without being shown how? | Reader derives non-obvious insight independently |
| **Compression** | Does the core claim hold in 1–2 memorable, repeatable sentences? | Reader would say it at dinner |

### Writing — Title / Tagline / Cover copy

Best for: book titles, product names, essay titles, positioning lines.

| Dimension | Question | What a 10 looks like |
|---|---|---|
| **Shelf-stop** | Does this generate immediate curiosity cold, with no context? | Reader cannot put it back |
| **Frame signal** | Does the title hint at the core concept without explaining it? | Title does conceptual work |
| **Genre fit** | Does it read in the right register (Harari/Toffler vs. consultant report)? | Belongs on that shelf naturally |
| **Compression** | Do title + subtitle together compress to a single repeatable unit? | "A book about X" in one breath |

### Strategy / Positioning / Pitch

Best for: product positioning, enterprise pitch, go-to-market framing, investor narrative.

| Dimension | Question | What a 10 looks like |
|---|---|---|
| **Differentiation** | Is the claim genuinely distinct from what's already in the market? | Buyer thinks "I've never heard it framed this way" |
| **Resonance** | Does it match a live pain the target audience is carrying right now? | Immediate recognition: "yes, that's my problem" |
| **Coherence** | Does the claim hold together under 5 minutes of stress-testing? | No obvious fatal objections |
| **Compression** | Can the target audience explain it in one sentence to their boss? | Buyer can relay it accurately |

### Essay / Op-Ed / Newsletter

Best for: Substack posts, op-eds, opinion pieces, newsletter angles.

| Dimension | Question | What a 10 looks like |
|---|---|---|
| **Hook** | Does the opening make stopping feel like a loss? | Reader cannot exit in the first 3 paragraphs |
| **Novelty** | Is there something here the reader hasn't seen framed this way? | "I haven't read this take before" |
| **Evidence tension** | Does it make a claim specific enough to be falsifiable, not just plausible? | Reader can imagine what would disprove it |
| **Compression** | Can the reader state the essay's argument in one sentence? | Retweetable claim |

### Abstract / Research Summary

Best for: paper abstracts, grant proposals, report executive summaries.

| Dimension | Question | What a 10 looks like |
|---|---|---|
| **Clarity** | Does the reader know exactly what was done and what was found? | No re-reading required |
| **Contribution** | Is the gap this fills and what it adds stated explicitly? | Reader can explain the "so what" |
| **Scope** | Are the boundaries of the claim clear — what it does and doesn't cover? | No overclaiming, no underclaiming |
| **Compression** | Does the abstract function as a standalone unit — title + abstract = complete picture? | Reader can cite it accurately without reading the paper |

---

## How to incorporate oracle-hints from the user

If the user passes `--oracle-hint "X matters"`, map their hint to a named dimension:

| User hint | Dimension to use or create |
|---|---|
| "Clarity matters" | Map to Compression or Clarity dimension |
| "Boldness matters" | Add boldness penalty: penalise hedged or softened versions |
| "Must be specific" | Add Precision dimension: does the claim name something falsifiable? |
| "My audience is [X]" | Use that as a persona type — create a reader with that profile |
| "Don't care about [Y]" | Drop or down-weight that dimension |

---

## Panel size guidance

| Task type | Panel size | Rationale |
|---|---|---|
| Writing / creative | 3 | Diverse enough, fast to evaluate |
| Strategy / pitch | 3–4 | Add a skeptic persona for stress-testing |
| Research / technical | 2–3 | Specialist + intelligent lay reader |
| Team-facing work | 3 | Representative stakeholder types |

Odd panel sizes prevent ties. 3 is the default. 5 for high-stakes convergence runs.

---

## Evaluator agent instruction

The evaluator must be a **strong model** (opus-bedrock preferred). Weak evaluators produce noisy scores and the loop fails to converge.

Never modify `evaluate.md` mid-run — this invalidates all comparisons. If the oracle needs changing, start a new run.
