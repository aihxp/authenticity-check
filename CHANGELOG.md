# Changelog

All notable changes to this skill are documented here. This project adheres
to semantic versioning.

## [1.0.1] - 2026-05-15

Post-release polish and verification hardening. No change to the band logic
or the diagnostic-only boundary; this release clarifies precedence, adds a
worked example and an eval case for the relocated-signature property, and
documents known limitations.

### Added

- Fifth worked example in `references/examples.md` for the
  relocated-signature case (clean vocabulary, uniform rhythm, no human
  markers): the canonical "laundered AI slop" demonstration, paired with
  Example 3 (restraint on careful human prose) so the two failure
  directions are visible side by side.
- Seventh case in `evals/evals.json` formalizing the relocated-signature
  regression in the runtime eval set (previously only in
  `evals/RESULTS.md` prose).
- Anaphora precedence note in `references/scoring.md` Part 1: a single
  anaphora is still a human rhetorical choice per `do-not-flag.md`, but
  anaphora used as the structural skeleton of a marker-free uniform
  passage is the template, and the Step 0b relocated-signature override
  governs.
- Regression-pass criteria and a "Known untested edge cases" section in
  `evals/RESULTS.md` (voice-deviation mode interacting with the override).

### Changed

- Split the dense Step 0b relocated-signature override paragraph in
  `SKILL.md` into three short paragraphs (trigger, rationale, action) for
  readability.
- Added Zed to the `AGENTS.md` enumeration of tools that read the file.
- `README.md`: footnote on the 13-tool count (Zed shares files with
  `AGENTS.md` / the Continue rule); short note that Pass 3 mostly engages
  on multi-paragraph inputs; clarification that the vendored-criteria sync
  stamp records the last criteria sync, not every humanizer commit.

## [1.0.0] - 2026-05-15

First stable release.

### Added

- Pure-prompt `authenticity-check` skill: `SKILL.md` plus on-demand
  reference files. No scripts, no dependencies, no network access. Tools are
  read-only (Read, Glob, Grep); the skill never edits.
- Diagnostic-only design: scores and flags, never rewrites. The rewrite is
  the separate `humanizer` skill's job. The split is deliberate: a combined
  score-then-rewrite loop is detector-gaming, which humanizer refuses, so
  diagnosis and transformation are kept as a human-judged pair, never merged.
- Output contract: an authenticity band (Reads human / Mixed signals / Reads
  AI-generated), a 0-100 authenticity score, span-level flags with reasons, a
  required "Reads as human" section, a score basis, a caveat, and a next
  step. No rewritten prose is ever returned.
- Multi-pass diagnostic: catalog scan (Pass 1), mandatory false-positive
  audit with veto power (Pass 2), read-only internal-consistency heuristics
  for stylometric-inconsistency and semantic-drift span detection (Pass 3),
  and voice-deviation analysis in voice-deviation mode (Pass 4).
- Density pre-check (Step 0b) that scales scrutiny to evidence so human-first
  text is not over-flagged, with chat-UI contamination as a decisive
  override.
- Native scoring rubric (`references/scoring.md`): the band vocabulary,
  the 0-100 forces, the do-not-flag demotion-and-credit asymmetry, and the
  read-only internal-consistency heuristic definitions.
- Native worked examples (`references/examples.md`): generic AI-heavy text,
  voice-deviation, a restraint case, and a reframed detector-evasion request.
- Vendored detection criteria, synced from the canonical `humanizer` repo:
  `references/tell-patterns.md`, `references/do-not-flag.md`, and
  `references/voice-matching.md`. Each carries a header stamp. Last synced
  2026-05-15 from humanizer commit `e9404c9`.
- **Vendored-criteria sync obligation (recorded here as a standing
  commitment):** the three vendored files are synced copies, not the source
  of truth. When humanizer's criteria change they must be re-synced; they
  must not be edited in this repo independently, or the diagnose/rewrite pair
  will drift. These copies and humanizer's originals are to be reconciled
  into a single shared source of truth when the `voiceprint` product
  (humanizer + authenticity-check, bundled) is built.
- voiceprint composability: no assumption this is the only skill installed,
  clean separation of the skill from its criteria, and non-colliding names
  (skill name, Cursor rule filename, and frontmatter `name` are all
  `authenticity-check`).
- Multi-tool support: Claude Code, Cursor, Codex, Antigravity, Gemini CLI,
  Pi Coder, OpenCode, GitHub Copilot, Windsurf, Cline, Continue, Zed, and
  Aider, via `SKILL.md`, `AGENTS.md`, `.cursor/rules/authenticity-check.mdc`,
  `GEMINI.md`, `.github/copilot-instructions.md`, `.windsurfrules`,
  `.clinerules`, `.continue/rules/authenticity-check.md`, and
  `CONVENTIONS.md`. Every adapter points the agent at the same `SKILL.md`
  and `references/`, so the workflow is identical across tools.
- Relocated-signature hardening. Step 0b carries a second density override
  (alongside the chat-UI-contamination override): clean, marker-free prose
  with uniform or templated rhythm is treated at high scrutiny rather than
  biased toward a high score, because absent slop vocabulary is the
  laundering, not evidence of a person. `references/scoring.md` makes the
  human-marker test decisive (Part 1: Mixed signals requires credited markers
  or a genuinely inserted region; uniform marker-free prose is Reads
  AI-generated even when vocabulary is clean), enforces band/number
  consistency (Part 2), and adds a symmetric restraint guard (Part 4) so
  genuine careful human prose is still not scored low.
- Verification eval set (`evals/evals.json`) plus a recorded blind
  verification battery (`evals/RESULTS.md`): the six suite cases, a
  known-vs-non-known battery, and the relocated-signature regression set, all
  run as blind isolated diagnoses. MIT license.
