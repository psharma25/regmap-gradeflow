# GradeFlow AI — Grading-Mark Specification

_Saved 2026-07-06 from the user's request._

## Verbatim request

> Please make sure marking is consistent on all grading sheets: (1) a right
> check for right answer, (2) a red cross for wrong answers; do not provide
> right answer (3) for corrected pregraded sheet - keep previous marks of check
> and cross, add a green circle for the corrected answer. (4) place these marks
> such that the marks do not cover actual data on the sheet. Make sure generated
> sheets have text such that is is not overlapping either. Also generate sheets
> for science. Save this prompt somehow

## Resulting specification (implemented in `index.html`)

1. **Right answer → green check (✓).** Consistent on every generated and graded sheet.
2. **Wrong answer → red cross (✗).** The correct answer is **not** shown on the sheet.
3. **Pre-graded / resubmission sheets** keep the previous pass's marks — a green ✓ on
   every problem that was right the first time and a red ✗ on every problem that was
   wrong — and add a **green circle (◯)** around each corrected answer.
4. **Placement:** all marks are positioned from the *measured* width of the student's
   answer, so a check, cross, or circle never lands on the prompt or the answer.
   Wide worksheet types (division-with-remainder, fractions, order-of-operations,
   linear equations, English, Science) render in a single column, and prompt sizes
   auto-fit their column, so generated problem text never overlaps.
5. **Selectable worksheet types** (Intake ▸ *Test sheet type*): Addition, Multiplication
   (2-digit), Division with remainder, Fractions, Order of operations, Linear equations,
   English, **Science** (fill-in-the-blank across life / earth / physical science), and
   **multi-line Calculus** — **Derivatives** (`d/dx` of a polynomial) and **Integrals**
   (`∫ p(x) dx`, indefinite, `+ C`) — plus the original Mixed B/C/D sample. Calculus
   problems render the expression on one line and the worked answer on the line below,
   and are graded by polynomial equivalence (the `+ C` on integrals is ignored). A
   wrong-answer-rate selector and a *pre-graded* toggle control each batch. The same
   marking format (1–4 above) applies to every type, including calculus.
6. **Deterministic grading:** every grade is recomputed by the engine from the answer key
   (integer / division-with-remainder / fraction-equivalence / text comparators). OCR or
   a connected model never sets a grade — the same seed reproduces the same sheet and score.

## Validation performed before delivery (jsdom + canvas mock)

- Grading is correct for all types: perfect papers grade 100%; all-wrong papers grade 0%
  (no distractor ever collides with the key). Calculus answer keys were additionally
  verified by numeric differentiation (derivative keys and integral antiderivatives).
- Normal sheets show ✓/✗ only — no answer key text anywhere on the page.
- Pre-graded sheets: a green ✓ for every first-right problem, a red ✗ for every first-wrong
  problem, and a green ◯ around every corrected answer.
- No ✓/✗ mark overlaps the student's answer; no problem-grid text overlaps.
- Output is deterministic and reproducible across reloads.
