# GradeFlow — captured requirement prompts (build spec)

These are the operator prompts that define this build. Treat them as the acceptance spec.

## Marking convention (locked)
- Green check (✓) — correct answer.
- Red cross (✗) — wrong answer. The correct answer is NEVER printed on any sheet.
- Green circle — drawn on/around the corrected answer on resubmission sheets ONLY,
  without changing any other existing marks (prior ✓/✗ stay as-is).
- Circled red score stamp on every auto-graded sheet.
- Marks are placed from measured answer widths so they never cover problem text or answers.

## Pipeline requirements
- Every uploaded scan is auto-rotated as needed (0/90/180/270 detection; manual ↻ override).
- Faint pencil is enhanced (contrast/gamma, optional binarize) before reading.
- Recognized Kumon worksheets (B53a, D11a, D102a, C61a, G81a, I121a, E71a, E72a, E73b)
  are graded against the deterministic KumonMath answer key — the model/OCR never sets a score.
- Unrecognized scans are auto-oriented and sent to output for hand grading (never guessed).
- One failing sheet never blocks the batch: it is skipped, named, and the rest continue.

## Sample tests
- Selectable subjects (math + English + science families), fresh / resubmission / faded pencil.
- Every generated sheet carries a green "SAMPLE TEST" badge and "not Kumon material" note.

## Negative tests
- Generator produces intentionally NOT-auto-gradable sheets:
  unreadable · ambiguous · blank · unrecognized · conflicting.
- Every negative test must route to "Needs grading" (human grading) — never auto-scored.
- The failure reason is notified: on the queue chip, in the orange manual-grading panel,
  in the review summary, and printed as a red banner on the sheet itself.

## UI requirements
- "Remove selected" removes the selected sheet(s): checkbox ticks, card-click selection,
  or the currently highlighted sheet; confirmation modal; persists across reload.
- Compressed single-line run-mode banner.
