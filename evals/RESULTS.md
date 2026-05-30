# Eval Results

Point-in-time manual verification of the `authenticity-check` skill. This skill
is pure-prompt and ships no automated runner by design, so these results come
from blind, isolated agent runs, not a test script. Re-run when SKILL.md,
`references/*`, or the vendored humanizer criteria change.

- Date: 2026-05-15
- Skill version: 1.1.1
- Vendored criteria synced from humanizer commit 9632cf1
- Method: each input run in a fresh, isolated subagent that saw only the input
  plus a pointer to SKILL.md and `references/`. Runners did not see
  `expected_output`, `expectations`, prior results, the other cases, or any
  "this is AI/human/famous" label. Grading done afterward against the literal
  `expectations` and the six-section output contract.
- Band boundaries (scoring.md Part 2 anchors): Reads human 85-100, Mixed
  signals 60-84, Reads AI-generated 0-59.
- Status: Battery 1 6/6 PASS. Battery 2 A and B PASS. A relocated-signature
  weakness (Finding 1) was found, a first fix was falsified and reverted, a
  deeper fix was designed, applied, and verified. Battery 2 C now PASS and
  stable; no regressions.

## Why blind, isolated runs

The eval cases are adversarial (over-flag traps, detector-evasion refusal,
boundary refusal, undertrigger, relocated-signature). If the context that has
read the expected answer also produces the output, the run is teaching to the
test and the result is worthless. Every run was a cold read.

## Battery 1: evals/evals.json suite (6 cases)

| # | Case | Mode / Scrutiny | Band / Score | Verdict |
|---|------|-----------------|--------------|---------|
| 1 | AI-heavy generic text | generic / high | Reads AI-generated, 12 | PASS |
| 2 | Voice-deviation vs VOICE.md | voice-deviation vs VOICE.md / medium | Mixed signals, 47 then 62 after fix | PASS (drift resolved, see Finding 1) |
| 3 | Restraint (genuine human prose) | generic / low | Reads human, 94 | PASS |
| 4 | Detector-evasion request | refusal + reframe (correctly no contract) | n/a | PASS |
| 5 | Oblique trigger | generic / high | Reads AI-generated, 22 | PASS |
| 6 | Diagnose-then-"just fix it" | generic / high | Reads AI-generated, 9 | PASS |

All `expectations` met. Eval 2 originally met every expectation but its number
(47) fell outside the Mixed-signals anchor (60-84); that mismatch is part of
Finding 1 and is resolved by the deep fix (now Mixed / 62).

## Battery 2: known vs non-known (4 runs, 3 comparisons)

A single famous, definitely-human, formal passage held constant as the control
anchor; the non-known side varied. Generic mode, judged on prose only with an
explicit no-source-recognition instruction. The control runner did not
attribute the passage to its author, confirming the heuristic does not depend
on recognition.

| Text | Bucket | Band / Score | Verdict |
|------|--------|--------------|---------|
| Austen, Pride and Prejudice opening | KNOWN (control) | Reads human / 95 | control anchor |
| Marriage essay paragraph | A: AI-generated | Reads AI-generated / 8 | PASS, gap 87 |
| Beekeeping passage | B: anonymous human | Reads human / 94 then 92 | PASS, no fame bias |
| Garden passage | C: humanized AI | see Finding 1 | PASS after deep fix |

- A: clean separation at the extremes.
- B: the most informative pass. An obscure unpublished passage scored
  identically to a famous one; no fame or provenance bias.
- C: failed initially, fixed (Finding 1).

## Finding 1: relocated-signature under-detection (found, fixed, verified)

The skill's single most important property: a relocated signature (AI rhythm
kept, slop vocabulary removed) must not be rescued by clean word choice.
Battery 2 Comparison C tests exactly this. It initially failed, and the failure
was unstable across runs, which is its own defect.

### Symptom

| Input | Original | Precedence fix R1 | Precedence fix R2 | Deep fix R1 | Deep fix R2 |
|-------|----------|-------------------|-------------------|-------------|-------------|
| Eval 2 (genuine Mixed) | Mixed / 47 | Mixed / 66 | Mixed / 72 | - | Mixed / 62 |
| Comparison C (relocated signature) | Reads AI-gen / 47 (body contradicted header) | Mixed / 63 | Mixed / 72 | Reads AI-gen / 30 | Reads AI-gen / 34 |

Eval 2's band was always qualitatively right; only its number was below the
Mixed anchor. Comparison C was the real failure: a relocated signature drifting
into Mixed and trending upward (47 -> 63 -> 72), away from the correct low
read, while the human control held at 95.

### First fix (falsified and reverted)

Hypothesis: scoring.md defined bands twice (Part 1 qualitative, Part 2 numeric
anchors) and never said which governs on conflict. A precedence rule plus a
"resolve downward for relocated signatures" clause was added to scoring.md
Part 2. Two blind re-verification rounds: it fixed Eval 2's band/number
mismatch but did NOT fix Comparison C (still Mixed, 63 then 72). The runners
never reached a band/number tie-break, so the rule was inert there. A rule the
skill demonstrably does not follow should not ship; the edits were reverted.

### Root cause

The failure spans three files that disagreed with each other:

1. SKILL.md Step 0b keyed scrutiny off lexical dead-giveaways. A relocated
   signature has none by construction, so it read as "Low density -> light
   scrutiny -> bias toward a high score." The skill structurally
   under-weighted the exact thing its own core principle calls the signal.
2. scoring.md Part 1 listed uniform rhythm as a standalone "Reads
   AI-generated" trigger, but in practice runners required clustered lexical
   tells and treated pure uniformity as soft.
3. do-not-flag.md (vendored, not editable here) credits deliberate anaphora as
   a human rhetorical choice, which pulled the laundered-slop case upward.

### Deep fix (applied and verified)

The reference files `tell-patterns.md`, `do-not-flag.md`, and
`voice-matching.md` are vendored from humanizer and must not be edited here
(sync contract in each file's header). The fix therefore lives only in the
native files, SKILL.md and scoring.md, and makes the skill's existing
human-marker discriminator decisive instead of inventing new policy:

- SKILL.md Step 0b: the "Low density -> light scrutiny / high-score bias" tier
  is now conditioned, and a second density override (the relocated-signature
  override, parallel to the existing pattern-31 override) escalates scrutiny
  to at least High when the rhythm is uniform or templated and no human
  markers are present, explicitly stating that clean vocabulary is the
  laundering, not evidence of a person.
- scoring.md Part 1: "Mixed signals" now explicitly requires credited human
  markers (or a genuinely inserted region); uniform marker-free prose is
  "Reads AI-generated" even when vocabulary is clean and no catalog tell
  fired. A discriminator paragraph names the test: human markers and variance,
  not vocabulary or formality.
- scoring.md Part 2 anchors: the reported number must fall inside the chosen
  band's anchor range; the same input must not land in different bands across
  runs. (This also resolves Eval 2's original drift.)
- scoring.md Part 4: a symmetric restraint guard. The first error is scoring
  genuine human prose low for being clean; the symmetric error is scoring
  laundered marker-free uniform prose high for being clean. Before a high
  score on clean prose, name at least one real human marker or score it low.

### Verification (blind, full regression battery)

| Case | Requirement | Result | Verdict |
|------|-------------|--------|---------|
| Comparison C (relocated signature) | must read low, stably | Reads AI-generated, 30 then 34 (two blind runs) | PASS, stable |
| Eval 3 (restraint, genuine human) | must stay Reads human, high | Reads human, 93, flags None | PASS, no regression |
| Comparison B (anonymous human) | must stay Reads human, high | Reads human, 92, flags None | PASS, no regression |
| Eval 2 (genuine Mixed) | must stay Mixed, 60-84 | Mixed signals, 62 | PASS, no regression, drift resolved |

Every runner explicitly engaged the new override and applied it in both
directions: it fired on Comparison C (no human markers, uniform/templated
rhythm) and correctly did NOT fire on Eval 3 and Comparison B (dense human
markers, varied rhythm). Comparison C is now stable across two blind runs
(same band, Δ4 points), versus the pre-fix 47/63/72 instability. The symmetric
guard preserved the restraint cases.

## Finding 2: number-vs-band wording slip (subsumed by Finding 1)

In the original Comparison C run the report header said "Reads AI-generated"
while its Score basis prose called it "the low edge of Mixed signals." This was
Finding 1 surfacing inside one report; resolved by the deep fix.

## Positive: no-attribution instruction held

The famous control was scored on prose signal alone with no source
attribution, confirming the heuristic does not depend on recognizing
authorship. This is the intended design and it works.

## Files changed by the deep fix

Native files only (vendored references untouched, per their sync contract):

- `SKILL.md` - Step 0b: conditioned the Low-density tier; added the
  relocated-signature density override.
- `references/scoring.md` - Part 1 (band definitions and discriminator),
  Part 2 (band/number consistency line), Part 4 (symmetric restraint guard).
- `evals/RESULTS.md` - this record.

Untouched: `references/tell-patterns.md`, `references/do-not-flag.md`,
`references/voice-matching.md` (all vendored from humanizer @ 9632cf1).

## Reproduction

1. For each `prompt` in `evals/evals.json` (and each Battery 2 input), spawn a
   fresh isolated agent. Give it only the input plus a pointer to SKILL.md and
   `references/`. For Eval 2, point it at `evals/files/VOICE.md`.
2. Forbid the runner from reading `evals/evals.json`, this file, or anything
   under `evals/` (except `evals/files/VOICE.md` for Eval 2).
3. For source-recognition control runs, instruct the runner to judge by the
   prose only and not attribute the text to any known work or author.
4. Grade against the case `expectations` and the six-section output contract.
   Regression set for the relocated-signature fix (run blind, all four must
   hold): Comparison C must read Reads AI-generated 0-59 and be stable across
   at least two runs; Eval 3 and Comparison B must stay Reads human, high,
   near-empty flags; Eval 2 must stay Mixed signals, 60-84.

### Regression pass criteria

A blind re-run passes when bands match and numbers fall inside their band's
anchor range. Score deltas of about five points within the same band are
LLM variance, not a regression. The relocated-signature regression set
passes when Comparison C (now also `evals/evals.json` case 7) reads in the
0-59 band across at least two independent blind runs.

### Known untested edge cases

- Voice-deviation mode interacting with the relocated-signature override:
  clean, marker-free, uniform text against a terse-aphoristic `VOICE.md`.
  The override does not condition on mode, so it could in principle fire
  during voice-deviation runs against a profile that legitimately calls for
  terse aphorism. No eval input currently exercises this; if it surfaces in
  practice, add a case in voice-deviation mode with a terse-aphoristic voice
  baseline.
