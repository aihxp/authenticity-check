# Text Provenance Signals

This file is native to the `authenticity-check` repo. Read it during the
Step 0a provenance preflight when the user asks about watermarks or hidden
marks, when a suspicious character is visible, or when classification is
uncertain.

The carrier taxonomy and context guardrails are adapted at the class level
from [watermarks-remover](https://github.com/guillaumemeyer/watermarks-remover),
version 0.4.0 at source commit `28eca2d91fd4`, inspected 2026-08-14 under its
MIT license. This skill keeps only the read-only inspection half. It does not
copy the remover workflow, clean files, or rewrite text. This source note
records a point-in-time taxonomy adaptation, not a fork, wrapper, service
integration, or promise of compatibility or feature parity with later
upstream releases.

## What this preflight can establish

Inspect the exact text supplied to the skill for Unicode characters that can
carry an edit-based or steganographic signal. Report a character only when
the current rendering or read tool exposes it reliably. Use escaped notation
such as `<U+200B>` in the report so an invisible finding is visible to the
reader. Never silently delete, normalize, substitute, or re-save the text.

This preflight cannot establish that a vendor inserted a character or that a
particular authoring system produced the text. The same codepoints can come
from typography, multilingual orthography, byte-order markers, copy and
paste, or damaged formatting.

## Candidate classes

Treat the following as candidates, not verdicts:

| Class | Representative codepoints | Default read |
| --- | --- | --- |
| Zero-width and format controls | U+00AD, U+034F, U+180E, U+200B-U+200F, U+2060-U+2064, U+FEFF, U+FFF9-U+FFFB | Suspicious inside ordinary ASCII prose; audit context first |
| Bidirectional controls | U+061C, U+200E-U+200F, U+202A-U+202E, U+2066-U+2069 | Suspicious in unidirectional prose; often legitimate in mixed-direction text |
| Tag characters | U+E0001-U+E007F | Suspicious unless they belong to a valid flag sequence |
| Variation selectors | U+180B-U+180D, U+FE00-U+FE0F, U+E0100-U+E01EF | Ambiguous; often load-bearing in visible sequences or a writing system |
| Space lookalikes | U+00A0, U+1680, U+2000-U+200A, U+202F, U+205F, U+3000 | Informational by default; editors and typesetting systems insert them routinely |
| Latin lookalikes | Cyrillic or fullwidth characters that resemble Latin letters | Inspect only on an explicit confusable-character request; never treat a real non-Latin word as suspicious |
| Other format characters | Unicode category `Cf` not covered above | Context-dependent; identify the exact codepoint before reporting |

Repeated controls, tag runs, or several carrier classes embedded between
plain ASCII letters are stronger evidence of deliberate encoding than one
isolated character. Space lookalikes remain weak evidence even when they
recur because typography can account for a whole document.

## Mandatory context audit

For every candidate, test the surrounding characters before reporting it as
suspicious:

- Preserve U+200C and U+200D when they join letters or marks in a script that
  uses them orthographically, including Persian and Devanagari.
- Preserve U+200D, U+FE0E, and U+FE0F when they are load-bearing parts of an
  emoji sequence. Do not reproduce the glyph in the report; name the sequence
  and codepoint.
- Preserve tag characters after an emoji base when they form a flag sequence.
- Treat U+0600-U+0605, U+06DD, U+070F, U+08E2, U+110BD, and U+110CD as
  potentially normal Arabic or Syriac format characters.
- Treat U+FEFF at the start of a file as a possible byte-order mark. The same
  codepoint inside a sentence is more suspicious.
- Treat no-break, thin, narrow, and ideographic spaces as typography unless
  their placement encodes a clear non-typographic pattern.
- Treat bidi controls as potentially necessary when right-to-left and
  left-to-right text are mixed. Report them as suspicious only when the text
  does not earn directional control.
- Treat Mongolian variation selectors and other script-specific selectors as
  orthographic when the surrounding script supports that use.

When the audit explains a candidate, record it as deliberately not flagged
inside `Provenance signals` if it helps the user understand the result. It is
not a prose-level human marker and does not belong in `Reads as human`.

## Confidence and reporting

Use these labels:

- **Probable carrier:** an exact non-space codepoint is confirmed in an
  unearned context, such as a zero-width character between ordinary ASCII
  letters or a repeated tag/control pattern.
- **Informational:** an exact ambiguous character is confirmed, such as an
  exotic space, but normal authoring or typography remains a strong
  explanation.
- **Deliberately not flagged:** the context audit identifies a legitimate
  orthographic, directional, emoji, flag, or file-start use.
- **Not inspectable:** the interface, rendered prompt, or read tool does not
  expose exact codepoints. Do not guess from visual spacing.

For each probable or informational finding, report the codepoint and Unicode
name, the count only if reliable, and a short escaped context. Do not show a
raw invisible character as the only evidence. Group repeated instances by
codepoint rather than flooding the report.

If nothing survives the context audit, say: `No suspicious text carriers
found in the inspectable text.` This means only that the available text did
not expose a deterministic carrier. It is not a clean certificate.

## Keep provenance separate from authenticity scoring

The authenticity score measures how the prose reads. A hidden character does
not change cadence, diction, specificity, or voice. Do not move the 0-100
score solely because the preflight found or did not find a carrier. If the
same text also contains prose-level tells, score those tells through Passes
1-4 and explain the two evidence channels separately.

Never infer statistical token-sampling watermarks from ordinary word choice.
Those signals require a scheme-specific detector, secret key, or controlled
generation context. This pure-prompt skill also does not inspect C2PA, EXIF,
XMP, document properties, pixel-domain marks, audio, or video. State these
coverage limits in the report whenever provenance is the user's main concern.
