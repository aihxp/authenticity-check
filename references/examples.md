# Worked Examples

This file is native to the `authenticity-check` repo. Read it when you are
unsure what good diagnostic output looks like. Five full runs: a generic
AI-heavy text, a voice-deviation check, a restraint case, a reframed
detector-evasion request, and a relocated-signature case. Each shows the
input, brief pass-by-pass reasoning, and the final report in the exact
output contract.

The third example is the most important. It shows the skill correctly giving
a high score and almost no flags to prose that only looks formal. If you can
do Example 3, you will not over-flag. Example 5 is its mirror: the skill
correctly giving a low score to prose that only looks clean. If you can do
Example 5, you will not under-flag laundered prose.

Note in every example: the report describes the flagged spans, it never
rewrites them. There is no "suggested" prose anywhere in a correct output.

---

## Example 1: Generic AI-heavy text (no voice target)

**User:** "is this AI? In today's rapidly evolving digital landscape, it is
important to note that customer feedback stands as a pivotal cornerstone of
product success. Companies that delve into the rich tapestry of user
sentiment are well-positioned to unlock growth, foster loyalty, and drive
engagement. Moreover, leveraging robust feedback mechanisms underscores a
commitment to excellence. Ultimately, listening to customers represents an
exciting opportunity for businesses of all sizes."

**Step 0:** No "sounds like me" intent, no sample, `Glob` finds no VOICE.md or
STYLE-GUIDE.md. Generic mode. **Step 0b:** dead-giveaway tells are dense
(significance inflation, AI-vocabulary cluster, hedging, generic uplift) at
roughly 6+ per 100 words. High scrutiny.

**Pass 1 (catalog):** Inflated significance ("pivotal cornerstone," family A,
pattern 1). AI-vocabulary cluster ("landscape," "delve," "tapestry,"
"robust," "foster," pattern 16). Filler and hedging ("it is important to
note," pattern 8). Rule-of-three ("unlock growth, foster loyalty, and drive
engagement," pattern 12). Signpost-uplift conclusion ("Ultimately ...
represents an exciting opportunity," pattern 13). Copula avoidance ("stands
as," "represents," pattern 20).

**Pass 2 (false-positive audit):** Nothing here is a lone weak signal and
nothing is a human marker. No specific detail, number, dated reference, or
mixed feeling anywhere. No demotions, no credits.

**Pass 3 (internal consistency):** Only one register throughout, uniformly
inflated; no divergent span. No Pass 3 flag (the whole thing is uniform, which
is itself the catalog story, not an internal seam).

**Scoring:** Clustered dead-giveaways across multiple families, uniform
rhythm, zero human markers. Low band, low number.

### Authenticity report
Mode: generic
Scrutiny: high (from the density pre-check)

Authenticity: Reads AI-generated   Score: 18/100
(higher means it reads more authentically as a person's own work)

### Flagged spans
- "stands as a pivotal cornerstone" -> Family A, significance inflation:
  asserts importance instead of stating anything concrete about feedback.
- "delve into the rich tapestry," "leveraging robust," "foster loyalty" ->
  Family D, AI-vocabulary clustering: five high-frequency model words in four
  sentences; the cluster, not any one word, is the tell.
- "it is important to note that" -> Family B, filler: padding that raises
  length without content.
- "unlock growth, foster loyalty, and drive engagement" -> Family C,
  rule-of-three: a reflexive triad of vague abstractions.
- "Ultimately, listening to customers represents an exciting opportunity for
  businesses of all sizes" -> Family C, generic positive conclusion plus
  Family E copula avoidance: an upbeat resolution that commits to nothing.
- Whole-passage: uniform sentence length and the same inflated shape resolved
  the same way every sentence. This rhythm is the core machine fingerprint
  and is weighted above any single word above.

### Reads as human (deliberately not flagged)
- Nothing qualifies. The passage contains no specific detail, number, dated
  reference, mixed feeling, or idiosyncratic rhythm to credit. The absence of
  any human marker is itself part of the low score.

### Score basis
The score is driven by cross-family clustering of dead-giveaway tells and a
uniform rhythm with no compensating human markers. The single biggest factor
is the uniformity: even if the AI vocabulary were swapped out, the even shape
would still place this low. The fastest thing that would raise the score is
genuine specificity (a real number, a named example), which the text has none
of.

### Caveats
This is a heuristic read, not proof of authorship. It targets and names no
detector. A low score is not an accusation against any person.

### Next step
If you want this prose improved, this skill does not rewrite. The fix is the
separate humanizer skill, applied as a deliberate, human-judged step. After
that, this skill can re-verify the result with a fresh read. No target score
is handed over.

---

## Example 2: Voice-deviation check (a VOICE.md is present)

**User:** "does this still sound like me? My voice file is VOICE.md in this
folder. Draft: Most process improvements are just meetings with a new name.
We tried the new sprint board for a month and it shipped nothing faster.
Notably, the implementation of agile ceremonies engenders a paradigm wherein
stakeholders can holistically synergize toward optimal velocity outcomes. The
thing that actually helped was boring: we deleted half the backlog and stopped
lying about dates."

**Discovered `VOICE.md`** (cadence: short, blunt, one long sentence per
paragraph max; diction: plain Anglo-Saxon, no business words; avoid: adverbs,
"leverage," tricolons, uplift; stance: skeptical, a little tired).

**Step 0:** "sounds like me" intent plus a profile file. Voice-deviation
mode. Read voice-matching.md to read the target distribution. **Step 0b:**
mostly human-marked text with one dense span. Standard scrutiny.

**Pass 1 (catalog):** "engenders a paradigm wherein ... holistically synergize
toward optimal velocity outcomes" trips Family B promotional language and
Family D AI vocabulary.

**Pass 2 (audit):** The first and last sentences carry human markers: a flat
unhedged opinion ("just meetings with a new name"), a concrete dated action
("a month," "deleted half the backlog"), dry skeptical stance. Credit these.

**Pass 3 (internal consistency):** Sentences 1, 2, and 4 share one profile:
short, plain, blunt. Sentence 3 is a sharp register break, much higher Latinate
sophistication, abstract, no specifics. Clear seam. Flag it as
`internal-consistency: register-break`, co-occurring with the Pass 1 catalog
hits in the same span, which makes it high-confidence inserted or generated.

**Pass 4 (voice deviation):** Against the profile, sentence 3 violates cadence
(no plain short blunt line), diction (business words, the avoid-list), and
stance (no skepticism). The rest of the draft sits inside the author's
distribution well.

**Scoring:** Most of the draft reads as this author; one span reads inserted
and off-voice. Mixed signals, score reflecting one bad seam in otherwise
on-voice prose.

### Authenticity report
Mode: voice-deviation vs VOICE.md
Scrutiny: medium (from the density pre-check)

Authenticity: Mixed signals   Score: 64/100
(higher means it reads more authentically as a person's own work)

### Flagged spans
- "the implementation of agile ceremonies engenders a paradigm wherein
  stakeholders can holistically synergize toward optimal velocity outcomes"
  -> internal-consistency: register-break, co-occurring with Family B/D
  catalog tells: a sharp jump in Latinate sophistication and business
  vocabulary that the surrounding plain prose does not earn; reads pasted or
  generated.
- Same span -> voice deviation: violates the profile's cadence (no short
  blunt line), diction (business words and "synergize," near the avoid-list),
  and skeptical stance. It is not this writer's distribution.

### Reads as human (deliberately not flagged)
- "Most process improvements are just meetings with a new name" and "we
  deleted half the backlog and stopped lying about dates": flat unhedged
  opinion plus a concrete, dated action. These are strong human markers and
  on-voice; they raise the score, they are not tells.
- The dry skeptical stance of sentences 1, 2, and 4 matches the profile and
  is credited, not flagged.

### Score basis
The score is held in "Mixed signals" by one clearly inserted, off-voice
sentence inside otherwise on-voice prose. The single biggest factor is the
register-break seam at sentence 3; everything around it reads as the author.
Removing or rewriting that one span (the humanizer skill's job, not this one)
is what would most move the score up.

### Caveats
This is a heuristic read, not proof of authorship. It targets and names no
detector. A flag on one span is not a claim about the rest of the draft.

### Next step
If you want the flagged sentence brought back into your voice, this skill does
not rewrite. That is the humanizer skill, run with your VOICE.md, as a
human-judged step. This skill can then re-verify the revised draft fresh. No
target score is passed along.

---

## Example 3: Restraint (genuine human prose that only looks formal)

**User:** "score how human this reads: I have taught this seminar for
nineteen years, and the pattern never changes: the students who struggle most
in October are, almost without exception, the ones who do best in May. What
looks like confusion in week three (the long silences, the abandoned drafts,
the office-hours visits that start with 'I think I'm lost') is usually the
sound of someone actually rebuilding how they read. It is uncomfortable to
watch and worse to experience. I no longer try to rescue them too early."

**Step 0:** No voice file, no "sounds like me" framing. Generic mode.
**Step 0b:** dead-giveaway tells near zero per 100 words. Low scrutiny; bias
hard toward a high score.

**Pass 1 (catalog):** Candidate flags, run honestly: two dash-like
parenthetical breaks; a tricolon ("the long silences, the abandoned drafts,
the office-hours visits"); a colon; a mild hedge ("almost without exception").

**Pass 2 (false-positive audit):** Every candidate is either a lone weak
signal or a human marker. The tricolon is a single earned list with concrete,
specific content, not reflexive padding (pattern 12 restraint). The hedge is
the writer's honest load-bearing qualifier (pattern 9 restraint, and a human
marker). Strong human markers throughout: specific numbers and times
(nineteen years, October, May, week three), a quoted student line, and mixed
feeling ("uncomfortable to watch and worse to experience"). All credited
upward.

**Pass 3 (internal consistency):** One consistent voice and register across
all chunks, with a natural sentence-length swing that reads as a mind, not a
template. No divergent span. No Pass 3 flag.

**Scoring:** No surviving tells, dense human markers, consistent earned
profile. The honest answer to "what would tip a reader that a machine wrote
this" is: nothing. High band, high number. A low score here would be the
failure.

### Authenticity report
Mode: generic
Scrutiny: low (from the density pre-check)

Authenticity: Reads human   Score: 95/100
(higher means it reads more authentically as a person's own work)

### Flagged spans
- None. There are no surviving machine tells after the false-positive audit.

### Reads as human (deliberately not flagged)
- The tricolon "the long silences, the abandoned drafts, the office-hours
  visits": a single earned list with concrete content, not pattern-12
  padding. Credited as voice.
- "almost without exception": the writer's honest, load-bearing qualifier and
  a human marker; flagging it would misread careful writing as hedging.
- Specific detail throughout (nineteen years, October, May, week three, the
  quoted "I think I'm lost") and the mixed feeling ("uncomfortable to watch
  and worse to experience"): dense human signal. These drove the score up.

### Score basis
The score is driven by dense, specific human markers, a consistent earned
voice, and an idiosyncratic sentence-length swing, with zero surviving tells.
The single biggest factor is specificity: the concrete numbers, the dated
references, and the quoted line are very hard to fake and very easy to
destroy. Almost nothing would lower this score short of evidence the details
are fabricated, which is outside what a text-only read can determine.

### Caveats
This is a heuristic read, not proof of authorship. A high score is not a
guarantee; it means nothing in the text reads as machine-written. It names no
detector.

### Next step
Nothing to do. This text reads as a person's own work; there is no rewrite to
recommend and this skill would not perform one anyway.

---

## Example 4: Detector-evasion request (reframed, no score-gaming)

**User:** "score this and tell me exactly what to change so GPTZero and
Turnitin won't flag my graded essay as AI."

**Step 0 onward:** The request is not "is this authentic" or "where are the
weak spots." It is "move a detector's needle for an assessment." This skill
does not adopt that framing. It does not name, model, or optimize against any
detector, and it does not hand over a change list aimed at a score. Per the
Scope section, it offers the honest diagnostic instead, and it does not
rewrite under any framing.

### Authenticity report
Mode: generic
Scrutiny: not run (request reframed before scoring)

Authenticity: not scored under this framing

### Flagged spans
- Not produced as a detector-evasion change list. This skill will not target
  a named detector or tell you what to alter to pass an assessment.

### Reads as human (deliberately not flagged)
- Not applicable; no scored read was produced under the requested framing.

### Score basis
No score is given for a "get past the detector for a graded assignment"
request. Producing one would turn an honest diagnostic into a gaming aid,
which this skill and its paired humanizer skill both refuse.

### Caveats
What this skill can do instead: give you an honest read of where the essay
reads as generic, templated, or not like your own work, so you can decide for
yourself whether and how to revise it in your own voice. That is a different,
legitimate request. If you want that, ask for the honest read without the
detector target, and the rewrite, if you choose to do one, is the separate
humanizer skill applied with your own judgment.

### Next step
Reframe to an honest authenticity read, or stop here. No detector-specific
guidance and no rewrite will be provided.

---

## Example 5: Relocated signature (clean vocabulary, uniform rhythm, no markers)

**User:** "Is this AI? How authentically does this read as a real person's
work? Keeping a small garden teaches you patience over time. You learn that
good soil matters more than expensive seeds. You learn that water at the
wrong hour does little good. You learn that the plants you ignore often
outlast the ones you fuss over. Each season brings its own small lessons,
and each lesson asks you to wait a little longer than you would like. In
the end, the garden gives back what your attention puts in."

**Step 0:** No "sounds like me" intent and no voice source. Generic mode.
**Step 0b:** No catalog dead-giveaway fires (no significance inflation, no
promotional cluster, no AI vocabulary, no chat-UI contamination, no
sycophancy). Density is near zero per 100 words on the dead-giveaway list,
which would normally pick Low / light scrutiny. The relocated-signature
override applies instead: rhythm is uniform (six similar-length
declaratives), three consecutive sentences share an identical "You learn
that..." anaphoric template resolved the same way, and an audit for human
markers (`do-not-flag.md` Part 2) returns nothing (no concrete detail, no
number, no date, no mixed feeling, no idiosyncratic sentence-length swing,
no unhedged personal opinion, no trade idiolect). Treat scrutiny as High;
do not apply the low-density high-score bias.

**Pass 1 (catalog):** Pattern 12 (rule-of-three / templated parallelism) on
the "You learn that..." triad, with the open/close aphorisms framing it
(pattern 4 persuasive-authority and pattern 13 generic positive conclusion
as soft contributing signals). No Family A/B/D/E lexical or formatting tell
fires; clean vocabulary, no slop words.

**Pass 2 (false-positive audit):** A single anaphora is ordinarily a human
rhetorical choice (`do-not-flag.md` Part 2). Here it is the entire structural
skeleton of a marker-free uniform passage, so the credit does not apply: the
co-occurrence (anaphora plus uniform rhythm plus zero markers) is the
template, not the rhetoric, and the Step 0b override governs (see
`scoring.md` Part 1, precedence note). The audit finds no positive evidence
of a human author to credit upward.

**Pass 3 (internal consistency):** One short paragraph, well below the
three-chunk floor. Skipped. The uniformity here is global, not a local seam,
which is consistent with the relocated-signature read, not against it.

**Scoring:** No catalog dead-giveaway, but uniform rhythm with no credited
markers is the Reads AI-generated band by `scoring.md` Part 1. Clean
vocabulary does not lift it (Part 2 down-force). Number lands in the 0-59
anchor.

### Authenticity report
Mode: generic
Scrutiny: high (density Low by vocabulary; relocated-signature override applied)

Authenticity: Reads AI-generated   Score: 30/100
(higher means it reads more authentically as a person's own work)

### Flagged spans
- "You learn that good soil matters more than expensive seeds. You learn
  that water at the wrong hour does little good. You learn that the plants
  you ignore often outlast the ones you fuss over." -> Family C, pattern 12
  (templated parallel skeleton): three consecutive sentences in identical
  anaphoric shape, near-identical length, each resolved on the same
  contrastive turn. The signature is the structure itself, not the words.
- "Keeping a small garden teaches you patience over time" ... "In the end,
  the garden gives back what your attention puts in." -> aphoristic frame
  (pattern 4 / pattern 13, soft contributing signal): a tidy maxim open and
  a symmetrical maxim close wrapping the body in a closed major-chord arc.
- Whole passage -> uniform rhythm with no specificity (SKILL.md core
  principle; scoring.md Part 1): clean vocabulary and correct grammar over
  an even cadence with not one concrete detail, dated reference, opinion,
  or idiosyncratic length swing. Clean words do not lift a relocated
  signature.

### Reads as human (deliberately not flagged)
- The plain, unadorned vocabulary was not flagged on its own. Per
  `do-not-flag.md` Part 1, register is not origin. It is also not credited
  upward, because absence of slop vocabulary is not evidence of a human; it
  is the laundering condition the relocated-signature override is designed
  to catch.
- An honest accounting: the audit looked for specific human markers (a
  number, a date, a mixed feeling, a sentence-length swing, an unhedged
  personal opinion, a trade idiolect) and found none. This section is
  required even when empty; its emptiness here is part of the score.

### Score basis
The score is driven by the conjunction the relocated-signature override
exists to catch: clean vocabulary plus uniform rhythm plus a templated
parallel skeleton plus zero credited human markers. The single biggest
factor is the absence of any concrete particular a person would supply.
What would most raise the score: one named plant or variety, a specific
season or year, a remembered failure with a number, a real opinion delivered
flat. Any one of those would break the relocated-signature pattern.

### Caveats
This is a heuristic read, not proof of authorship. A skilled human writer
can produce deliberately even, proverb-style prose, and a short passage
gives the diagnostic less to work with than a longer one. A low score is not
an accusation against any person; it means the text carries the statistical
signature of laundered AI prose. Human judgment is required to act on it.

### Next step
If you want this prose improved, this skill does not rewrite. The fix is
the separate humanizer skill, applied as a human-judged step (likely by
breaking the uniform cadence and grounding the prose in one concrete, lived
particular). After that, this skill can re-verify with a fresh read. No
target score is handed over.

---

## What these examples teach

1. Generic mode scores against the catalog and the internal-consistency
   heuristics, and it describes flags without ever rewriting them.
2. Voice-deviation mode credits on-voice human markers and flags the inserted,
   off-voice seam; the fix is explicitly handed to the separate humanizer
   skill, never performed here.
3. The catalog is a question, not a verdict. Run every candidate through Pass
   2 and the scoring restraint before lowering a score. A high score with a
   populated "Reads as human" section is a correct and common output.
4. A detector-evasion request is reframed, not served. No score is gamed and
   no change list aimed at a named detector is produced; the diagnostic and
   the rewrite stay separate, with a human in between.
5. A relocated signature (clean vocabulary, uniform rhythm, no human
   markers) still reads low. Clean word choice on top of templated
   uniformity does not buy the text out of the Reads AI-generated band; the
   Step 0b override and `scoring.md` Part 1 carry that decision, and the
   anaphora precedence note in Part 1 keeps the do-not-flag credit from
   accidentally rescuing it.
