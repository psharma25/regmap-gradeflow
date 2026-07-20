# GradeFlow AI — standalone deployment package (v0.3-alpha)

Single-page worksheet grading console. Everything — deterministic grading engine,
synthetic samples, PDF export, reports, and OCR of uploaded scans — runs in the
user's browser. **No server-side compute, no installs for users, no data leaves
their machine.**

## What's new in this build (graded-overlay marking)
Grading now composites correct/incorrect marks onto the sheet itself, and shows
the correct answer for every wrong one:
- Inline on the scan — a green ✓ on right answers and a red ✗ ellipse with
  `→ <correct>` on wrong ones, anchored to each answer via Tesseract word boxes.
- A corrections band below the scan — always present and authoritative, listing
  every problem as `#n ✓ 85` or `#n ✗ 34 → 33`, plus the red score stamp and a
  deterministic-engine evidence footer. This is what carries the correct answers
  even when an OCR tier returns no coordinates (VLM / cloud paths).
- Uploaded scans now export to PDF (single and bulk), each page sized to its own
  aspect ratio so nothing is stretched. Scores still come only from the
  deterministic engine; the model/OCR never sets a grade.

## Contents
    index.html            the entire application
    vendor/               in-browser OCR engine (Tesseract WASM, served same-origin)
      tesseract.min.js
      worker.min.js
      core/               primary WASM cores (LSTM: SIMD + non-SIMD) — loaded first
      tesseract-core*.wasm.js   non-LSTM fallback cores (kept at vendor/ root)
      eng.traineddata     language data (fast variant)
      ai/                 optional in-browser handwriting model runtime
                          (transformers.js + ONNX Runtime WASM)

Keep `vendor/` next to `index.html` — the OCR tier loads it same-origin.
Without it the app still works fully for synthetic sheets; uploaded scans
route to the review queue with an explanation.

## Deploy — GitHub Pages (recommended, free)
    gh repo create gradeflow --public
    git init && git add . && git commit -m "GradeFlow AI alpha"
    git branch -M main && git remote add origin <repo-url> && git push -u origin main
Then: repo → Settings → Pages → Deploy from branch → main / root.
URL: https://<user>.github.io/gradeflow/  — send that link; recipients need nothing else.

## Deploy — AWS (S3 + CloudFront)
    aws s3 mb s3://gradeflow-alpha
    aws s3 sync . s3://gradeflow-alpha --exclude "README.md"
Front with CloudFront (HTTPS, caching; set default root object index.html).
No EC2 required — this package has no server component.

## OCR tiers (automatic, in this order)
1. Local Ollama vision model — optional, best handwriting quality (user's own machine).
2. In-browser AI model (WebGPU, experimental) — DEFAULT. Runs SmolVLM inside the
   user's browser tab via vendor/ai/ (transformers.js + ONNX Runtime, served
   same-origin). Model weights stream from huggingface.co on first use
   (~300 MB, cached by the browser afterwards). Needs Chrome/Edge with WebGPU;
   otherwise falls back to slow CPU or cascades to tier 3. Worksheets never
   leave the machine.
3. In-browser OCR engine (vendor/, Tesseract WASM) — zero download beyond ~8 MB,
   fully local; clear pen/print reads well, faint pencil routes to review.
4. Cloud OCR via your key-holding proxy (worker.js, Cloudflare) — OFF by default;
   sends images to OpenRouter, so enable knowingly.
5. Review queue — the honest fallback. Scores ALWAYS come from the deterministic
   engine regardless of tier.

## Do not deploy on GitHub Codespaces
Codespaces are authenticated dev environments that idle-stop; they are not a
hosting platform for end users.

Privacy: student IDs only, no names; all processing and storage stay in the
user's browser (localStorage).
