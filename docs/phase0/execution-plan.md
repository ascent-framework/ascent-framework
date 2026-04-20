# Phase 0 Execution Plan

**Date:** 2026-04-19  
**Status:** Active working plan  
**Scope:** Post-preregistration execution planning for ASCENT-G Phase 0

---

## Purpose

This document translates the registered ASCENT-G plan into an execution-ready
Phase 0 checklist.

It is intentionally operational rather than theoretical. The goal is to remove
ambiguity before the first end-to-end pilot run.

---

## Phase 0 Goal

Reach an experiment-ready state for Paper 1 by validating one complete
task-specific adaptation pipeline:

```
train -> extract update vector -> run SVD analysis -> save outputs
```

A successful Phase 0 does **not** require multi-task results. It requires one
registered-model pilot run that proves the pipeline is executable and produces a
usable update vector.

---

## Locked Decisions

The following decisions are treated as fixed for Phase 0 unless a later
amendment is explicitly documented.

| Item | Decision | Rationale |
|------|----------|-----------|
| Primary model | `Qwen2.5-1.5B-Instruct` | Registered primary model for ASCENT-G |
| Pilot task | `GSM8K` | Simplest high-value reasoning task for pipeline validation |
| Primary method | `GRPO` | Registered primary adaptation method |
| Adapter target | LoRA on `q_proj`, `k_proj`, `v_proj`, `o_proj`, `gate_proj`, `up_proj`, `down_proj` | Matches preregistration |
| Main success artifact | One valid task update vector | Minimum object needed for downstream analysis |
| Pilot success criterion | `train -> extract -> SVD` completes once on the primary model | Matches roadmap deliverable |

---

## Platform Strategy

### Default path

Use **Kaggle GPU** for the first complete pilot run.

Reasoning:

- Free quota is sufficient for a 1-task pilot
- Session behavior is more predictable than free Colab
- It supports the "one task per session" workflow if Phase 1 is later expanded

### Fallback path

Use **local RTX 4090** if Kaggle environment setup becomes the bottleneck.

This fallback is acceptable for Phase 0 because the core requirement is pipeline
validation, not platform purity.

### Deferred path

Use **Vast.ai / paid cloud** only if Phase 2 transfer experiments exceed local
or Kaggle capacity.

---

## Storage Strategy

Phase 0 artifacts should be saved in a way that survives notebook/session
restarts.

**Fixed for Phase 0:**

1. **Kaggle Dataset** — primary storage for adapter checkpoints and exported artifacts
2. **Google Drive** — backup mirror only; sync after each completed session
3. **Local repository** — small derived outputs and metadata only (run logs, provenance JSON, SVD summaries)

HuggingFace Hub is deferred until Phase 1 public release is needed.
Do not leave storage destination ambiguous across sessions — pick one path per artifact type and log it in the run report.

Large model checkpoints must not be committed to the repository.

**Required runtime log entries (per session):**

- GPU model assigned by Kaggle (`nvidia-smi` first line)
- Precision in use (`torch.cuda.is_bf16_supported()`)
- Per-step training time (enables cross-hardware comparison)

---

## Required Outputs

Phase 0 is considered complete only if all outputs below exist.

| Output | Description |
|--------|-------------|
| Training log | Run configuration, reward/loss traces, runtime notes |
| Adapter checkpoint | Trained task-specific adapter weights |
| Extracted update vector | Flattened/concatenated vector used for geometry analysis |
| Analysis output | SVD spectrum and `r_90` computation on the pilot artifact |
| Run note | Short summary of what worked, failed, and what to fix next |

---

## Pilot Acceptance Criteria

The pilot is a pass only if all conditions below are met.

1. `Qwen2.5-1.5B-Instruct` loads successfully in the target environment.
2. GRPO training runs to the planned stopping point without environment failure.
3. Adapter weights are saved and can be reloaded.
4. A non-degenerate update vector can be extracted from the trained adapter.
5. The extracted vector can be consumed by the analysis code.
6. At least one SVD-based diagnostic runs successfully on the pilot output.

If any one of these fails, Phase 0 remains incomplete.

---

## Explicit Non-Goals

The following do **not** count as Phase 0 completion:

- Gemma-2-2b-only smoke test without the primary model
- Successful training without extractable adapter outputs
- Successful extraction without downstream analysis
- Benchmark quality claims based on incomplete or ad hoc runs
- Any conclusion about H1a/H1b/H2 from the pilot run

---

## Open Operational Questions

These questions should be resolved before scaling from the pilot to the full
Phase 1 task set.

1. What is the smallest stable GRPO batch configuration on the chosen platform?
2. Are reward functions available and trustworthy for each registered task?
3. Does the chosen LoRA configuration produce informative, non-degenerate update
   vectors?
4. What checkpoint/export format should be standardized across all runs?
5. Which metadata fields are mandatory in every run log?

---

## Immediate Next Actions

The next concrete actions after this plan are:

1. Create the `ascent-geometry` code skeleton with `training/`, `extraction/`,
   `analysis/`, and `statistics/`.
2. Run an environment smoke test for `Qwen2.5-1.5B-Instruct` plus TRL/GRPO.
3. Implement a single-task GSM8K pilot harness.
4. Verify adapter export and update-vector extraction before tuning training.
5. Run one full pilot and write a short run report.

---

## Notes On Registration Scope

This document is an execution plan, not a replacement for the registered
preregistration document. Any deviation from the registered model, task set,
core hypotheses, or primary analysis path must be logged separately and labeled
appropriately in later reporting.
