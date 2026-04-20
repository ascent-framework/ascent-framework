# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

A research documentation repository for the **ASCENT framework** — a theory that task adaptation in pretrained LLMs is primarily a control problem over a pretrained capability substrate, not knowledge injection. There is no code here yet; all content is Markdown documents.

## Document hierarchy

| File | Role |
|------|------|
| `docs/framework/v6.1.md` | Canonical framework statement — source of truth for all claims |
| `docs/preregistration/v1.3.md` | OSF pre-registration document — methodology and falsification criteria |
| `docs/paper1/outline.md` | Paper 1 structure and writing plan |
| `docs/paper1/evidence-mapping.md` | Prior work mapped to ASCENT claims |
| `docs/phase0/execution-plan.md` | Active Phase 0 execution checklist and operational plan |
| `docs/exploration/` | Non-canonical, non-registered speculative content |
| `docs/archive/` | Predecessor project documents (read-only reference) |
| `ROADMAP.md` | 30-month phased plan with decision gates |

Reading order: `framework/v6.1.md` → `preregistration/v1.3.md` → `paper1/evidence-mapping.md` → `paper1/outline.md`.

## Key distinctions to preserve

- **Registered vs. exploratory:** H1a, H1b, H2 are pre-registered primary hypotheses. H1c and H3 are exploratory. Content in `docs/exploration/` is explicitly non-canonical and must not be cited as registered claims.
- **Claim scope:** The central claim is task-distribution relative — do not generalize to "all adaptation" or "universally."
- **Framework version:** Always refer to v6.1 as canonical. The evolution history is in `docs/framework/evolution.md`.

## Repository organization (multi-repo context)

This repo (`ascent-framework`) holds theory and docs only. Code lives in separate repos:
- `ascent-geometry` — Paper 1 experiments: https://github.com/ascent-framework/ascent-geometry
- `ascent-arch` — Paper 2 prototype (planned M13+): https://github.com/ascent-framework/ascent-arch

## Current status (as of 2026-04-19)

Phase 0 — Foundation. OSF pre-registration v1.3 is public. No experiments running yet; current work is execution planning for the first end-to-end pilot.
