---
name: authenticity-check
description: Score how authentically text reads as a real human author's work and flag the AI-generated, AI-templated, or derivative spans. Use whenever the user wants to know if text is authentic, whether it reads like AI or like a person, how human a passage sounds, which parts sound machine-written, or whether their draft still sounds like them or a named author. Apply even when they do not say "authenticity check." Do NOT use for transformative requests (humanize, de-slop, rewrite, fix, make it sound like X); those belong to the separate humanizer skill.
alwaysApply: false
---

# authenticity-check (Continue / Zed rule)

When this rule is engaged, act as the `authenticity-check` skill defined in
this repository. It is pure-prompt instructions that score how authentically
a piece of text reads as the work of a real human author and flag the spans
that read as AI-generated, AI-templated, or generically derivative. No
scripts, no dependencies, no network access; tools are read-only. It is the
evaluative counterpart to the separate `humanizer` skill: this one diagnoses,
it does not rewrite.

Read `SKILL.md` at the repository root and follow it exactly, with
`references/tell-patterns.md` (32 patterns, six families),
`references/do-not-flag.md`, and, in voice-deviation mode only,
`references/voice-matching.md`. Run the method as written: Step 0 baseline
discovery, Step 0b density pre-check, then the multi-pass diagnostic (catalog
scan, mandatory false-positive audit with veto power, read-only
internal-consistency heuristics, voice deviation if a voice target exists).
Produce the exact output contract: Authenticity report (band plus 0-100
score) / Flagged spans / Reads as human / Score basis / Caveats / Next step.

Hard rule: this skill scores and flags only. It never rewrites, edits, or
returns improved prose, not even one suggested span; the rewrite is the
separate `humanizer` skill's job, run as a human-judged step, with no target
score carried into it. A combined score-then-rewrite loop is detector-gaming,
which humanizer refuses. This skill is not for defeating AI-detection
systems; reframe such requests toward an honest read of quality and voice.

`references/tell-patterns.md`, `references/do-not-flag.md`, and
`references/voice-matching.md` are synced copies vendored from the canonical
`humanizer` repo. Do not edit them here; re-sync from humanizer when its
criteria change (see each file's header stamp and the README).
