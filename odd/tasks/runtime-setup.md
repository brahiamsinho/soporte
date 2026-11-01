# Runtime setup

## Objective
Make the dogs-and-cats classifier runnable locally: recover its matching TensorFlow.js model, serve the camera demo from `localhost`, and preserve a reproducible training path.

## Problem
The browser frontend requested `model.json`, but the repository initially contained only two raw binary shards. The original model manifest and Python/Jupyter environment were missing.

## Scope
- Install pinned Python/Jupyter training dependencies in `.venv` and record them in `requirements.txt`.
- Recover the original model manifest only after verifying it corresponds to the existing shard files; do not fabricate a manifest.
- Update the browser for observable model/camera loading failures and document local use plus the optional notebook training route.
- Validate the local HTTP server and programmatic model loading; document the one manual camera check that requires a browser permission prompt.

## Constraints
- A new Git repository exists but has no commits. The user explicitly chose to retain it; do not commit unless they separately request it.
- Camera access requires `localhost` or HTTPS and user permission.
- The user authorized a Cats vs Dogs download (~787 MiB) and local training only as a fallback; prefer a verified recovery of the original model to meet the time constraint.
- TDD mode: disabled (no established test runner); use observed functional checks.

## Delivery strategy
ask-on-risk; forecast: 120–250 authored lines plus the recovered model manifest.

## Tasks
- [x] RT-1 — Map runtime requirements and verify the environment. Route: inline; evidence: Python 3.11.9 and Node 25.8.1 available; required Python packages and `model.json` absent.
- [x] RT-2 — Install and verify the local Python/Jupyter runtime. Route: delegated; evidence: required imports print `imports-ok`; `python -m jupyter --version` reports JupyterLab 4.2.5. TensorFlow.js conversion imports use a Windows-only TFDF shim; TFDF conversion is unsupported.
- [x] RT-3 — Recover and verify a TensorFlow.js manifest matching the existing shards. Route: delegated; evidence: each shard and `model.json` are byte-identical to the upstream reference by SHA-256; declared model float32 bytes equal the local shard total (5,491,492 bytes).
- [x] RT-4 — Harden browser loading/camera behavior and document local running. Route: delegated; evidence: visible status/error UI, model/video readiness gating, tensor disposal, and Windows localhost guide added.
- [x] RT-5 — Prevent stale camera requests during camera switching. Route: delegated; evidence: each request is versioned; stale acquired streams are stopped before UI state can change; JS syntax check and whitespace check passed.
- [x] RT-6 — Independently validate the repaired camera-switch behavior plus HTTP/model checks. Route: delegated; evidence: SHA-256, manifest-byte, JS syntax, and localhost HTTP checks passed; no stale camera state defect found in static review.

## Acceptance criteria and checks
- [x] `.venv` imports TensorFlow, TensorFlow Datasets, TensorFlow.js, Jupyter, NumPy, Matplotlib, and OpenCV.
- [x] `model.json` is a valid TF.js LayersModel manifest whose referenced shards match the local files by SHA-256.
- [x] `index.html` loads the model, handles model/camera errors, and receives an input tensor compatible with the model.
- [x] A stale camera request cannot replace the stream selected by a later request; stale streams are stopped.
- [x] A local HTTP server returns the page and every referenced model asset.
- [ ] Manual browser check: local server started at `http://localhost:8000` (PID 887); grant camera permission and confirm inference plus camera switching. This requires a real device/browser permission prompt and was not simulated.

## Progress
- 2026-03-21: Original setup tracking was incomplete after a prior dependency-install attempt timed out; `.venv` exists but has no requested packages.
- 2026-03-21: Reconciled the task document with current source evidence. Engram recovery mirror is pending because its local provider is unavailable.
- 2026-03-21: Native review assessment was unavailable before edits (`rdd: off`); after writer completion, treat verification as high-risk: self-verification plus independent verification.
- 2026-10-15: A delegated worker failed before execution due to provider transport (`WebSocket error` / `fetch failed`). State inspection found a new uncommitted Git repository and `requirements.txt`; the user explicitly authorized retaining both.
- 2026-10-15: The user explicitly authorized the Cats vs Dogs dataset download (~787 MiB) and local training.
- 2026-10-15: RT-2 passed. Installed TensorFlow 2.15.1, TFDS 4.9.4, TensorFlow.js 4.17.0, JupyterLab 4.2.5, NumPy 1.26.4, Matplotlib 3.8.4, OpenCV 4.10.0.84, and importlib-resources 6.4.5. A `.venv`-only shim enables TensorFlow.js Keras conversion because TF Decision Forests lacks a Windows wheel; `pip check` retains that intentional missing-distribution warning.
- 2026-10-15: RT-3 and RT-4 passed writer verification. Upstream/local SHA-256 matches: shard 1 `68f45104b40fdbf912b8b582624218e8efa3c2884fd76ce6a8541941cbbb1586`; shard 2 `b8d352c5836a4ca2d5d96145e67a5195d6b728de7812af3e5fee23be17705cd6`; manifest `82fc98aaf8bccad8beea06cfd0d0b28fdfcdbf03a3f33f8334998cd7fba7b8df`. Writer HTTP checks returned 200 for the page, manifest, and both shards.
- 2026-10-15: Independent validation initially found a camera-switch race: older `getUserMedia` calls could replace the intended stream. RT-5 added a monotonic request version and stops stale streams; `node --check` and `git diff --check` passed.
- 2026-10-15: RT-6 independent validation passed. The manifest declares 5,491,492 bytes, matching the two local shards; JavaScript syntax passed; `index.html`, `model.json`, and both shards each returned HTTP 200. Static review found no remaining stale camera request path.

## Next step
Await the user's manual browser camera check at `http://localhost:8000`. The server remains on PID 887; stop it with `taskkill /PID 887 /F` when finished.
