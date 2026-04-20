# ASCENT Research Roadmap

**30-Month Plan | Hobby Track | RTX 4090 + Cloud | 7.5h/week**  
Last updated: 2026-04-18

---

## Overview

```
Year 1 (M0–12):   Paper 1 (ASCENT-G) — Adaptation Geometry
Year 2 (M13–24):  Paper 1 finalized + Patent + Paper 2 start
Year 3 (M25–30):  Paper 2 prototype + Portfolio
```

```
Timeline:
M0    M2    M5    M10   M14   M18   M22   M26   M30
│     │     │     │     │     │     │     │     │
├─Ph0─┤─Ph1─┤──Ph2─────┤──Ph3─────┤─Ph4──────────┤
Setup H1a   H1-H2  Paper Draft  Submit  Patent  Conf  P2 proto
```

---

## Milestones

| ID | Month | Deliverable | Success Criterion |
|----|-------|-------------|-------------------|
| M0 | 0 | Project kickoff | Roadmap committed to GitHub |
| M1 | 2 | Phase 0 complete | OSF registered + TinyLoRA reproduces ±5% |
| M2 | 5 | H1a decision | Pass/marginal/inconclusive/fail logged |
| M3 | 10 | H1-H2 complete | All primary analyses done, claim tier determined |
| M4 | 14 | Paper 1 draft | Full manuscript, all sections written |
| M5 | 18 | Paper 1 submission | Workshop paper + arXiv preprint |
| M6 | 22 | Patent filed | Samsung IP process complete |
| M7 | 26 | Conference paper | Main conference accepted (or revised) |
| M8 | 30 | Paper 2 prototype | ASCENT-A initial architecture running |

---

## Phase 0: Foundation (Month 0–2)

**Goal:** Experiment-ready state. Every dependency resolved before Phase 1.

**Budget:** 60 hours total (30h/month)

### Month 1

| Week | Hours | Task |
|------|-------|------|
| 1 | 7.5h | Pre-registration v1.2 final → OSF registration |
| 2 | 7.5h | GitHub organization setup (see SETUP.md) + env check |
| 3 | 7.5h | TRL/GRPO install, Qwen2.5-1.5B load test, VRAM profile |
| 4 | 7.5h | Evidence mapping table + related work reading |

### Month 2

| Week | Hours | Task |
|------|-------|------|
| 5–6 | 15h | Code skeleton: training/, extraction/, analysis/, statistics/ |
| 7 | 7.5h | Pilot: GSM8K × Qwen2.5-1.5B end-to-end |
| 8 | 7.5h | TinyLoRA reproduction check; Phase 0 review |

### Deliverables

- [x] OSF pre-registration v1.2 public (https://osf.io/qgxyj/overview)
- [x] GitHub org + 3 repos created
- [ ] TinyLoRA reproduces GSM8K ≥ 85% with ≤ 100 params on 1.5B
- [ ] Full pipeline: train → extract → SVD working on 1 task

### Blockers to resolve in Phase 0

- GRPO memory footprint on RTX 4090 with 1.5B (target: batch_size ≥ 1)
- Reward function for each task (verify open-source implementations)
- LoRA configuration that gives meaningful update vectors (not dead weights)

---

## Phase 1: H1a First Test (Month 3–5)

**Goal:** Determine whether shared adaptive subspace exists.

**Budget:** 90 hours total

### Month 3

| Week | Hours | Task |
|------|-------|------|
| 9–10 | 15h | Tasks 1–4: GSM8K, MATH, HumanEval, CommonsenseQA |
| 11–12 | 15h | Tasks 5–8: AIME, MBPP, HellaSwag, ARC-Challenge |

*Runtime estimate: ~3h training per task on RTX 4090 = 24h total. Remaining: monitoring + debugging.*

### Month 4

| Week | Hours | Task |
|------|-------|------|
| 13–14 | 15h | Tasks 9–10: AMC, MATH500. Extract all update vectors. |
| 15–16 | 15h | SVD analysis: compute ρ, bootstrap CI, tier determination |

### Month 5

| Week | Hours | Task |
|------|-------|------|
| 17–18 | 15h | H1a decision + documentation. Decision tree execution. |
| 19–20 | 15h | Branch: Strong pass → Phase 2. Marginal → expand N. Fail → Recovery. |

### Decision Tree

```
H1a result:
├── Strong pass (ρ < 0.30, CI upper < 0.35)
│     → Phase 2 full track
├── Marginal pass (0.30 ≤ ρ < 0.50)
│     → Expand N to 15 tasks, rerun H1a
│     → If still marginal after N=15: continue to Phase 2 with qualified claims
├── Inconclusive (0.50 ≤ ρ < 0.70)
│     → Debug: check normalization, LoRA config, task diversity
│     → Rerun with different hyperparameters
│     → If still inconclusive after N=20: proceed to Recovery path
└── Fail (ρ ≥ 0.70, CI lower ≥ 0.65)
      → Recovery path (Paper on TinyLoRA mechanism)
```

### Deliverables

- [ ] 10 task update vectors stored (format: numpy .npy, one file per task)
- [ ] H1a result document (ρ value, CI, tier, decision)
- [ ] Blog post #1: "First Look at ASCENT-G Hypothesis"

---

## Phase 2: H1b + H2 (Month 6–10)

**Goal:** Complete all primary registered analyses. Determine final claim tier.

**Budget:** 150 hours total

### Month 6–7: H1b Analysis

| Week | Hours | Task |
|------|-------|------|
| 21–24 | 30h | H1b: projection onto V_k, pairwise cosine, statistics |

Additional analysis:
- Visualize update vectors in 2D/3D (PCA)
- Identify which tasks cluster together
- Exploratory: do task clusters correspond to domain (math vs code vs language)?

### Month 8–9: H2 Transfer

| Week | Hours | Task |
|------|-------|------|
| 25–28 | 30h | Primary: 1.5B-Instruct → 1.5B-base transfer |
| | | Secondary: cross-family (if compute allows) |

**Cloud compute:** ~$100-200 for secondary transfer experiments on 3B model (Vast.ai)

### Month 10: Synthesis

| Week | Hours | Task |
|------|-------|------|
| 29–32 | 30h | Combine all results, final claim tier, main figures |

### Deliverables

- [ ] H1b result (mean |cos|, max |cos|, tier)
- [ ] H2 transfer results (Retention %, tier)
- [ ] Final claim tier determined and documented
- [ ] 5 main figures ready (ready for paper)
- [ ] Blog post #2: "ASCENT-G: Adaptation Geometry Results"

---

## Phase 3: Paper 1 Writing + Patent Start (Month 11–18)

**Goal:** Paper 1 submitted to workshop + arXiv. Patent process started.

**Budget:** 240 hours total (split: 80% paper, 15% patent, 5% portfolio)

### Month 11–12: Draft v1

Section order:
1. §3 Framework (copy from v6.1.md — mostly done)
2. §4 Methodology (copy from pre-registration — mostly done)
3. §5 Experiments (results from Phase 2)
4. §1 Introduction (write last — story becomes clear)
5. §2 Related Work (expand from evidence-mapping.md)
6. §6 Analysis (exploratory results)
7. §7 Discussion + §8 Conclusion

### Month 13–14: Draft v2 + External Feedback

Options for external feedback (solo researcher):
- Post preprint to arXiv, tweet/post asking for feedback
- Email 1-2 researchers who cite TinyLoRA or SAE papers
- ACL/NeurIPS Discord communities (ML Research)
- OpenReview community forums

### Month 15–16: Patent Preparation

Tasks:
- [ ] Update documents in `docs/archive/operator-coordinate-compression/`
  with v6.1 framework language
- [ ] Add SpectralQuant to prior_art_mapping.md (critical missing entry)
- [ ] Rewrite claim_candidates.md as actual claim format
- [ ] Samsung IP team initial consultation (informal)
- [ ] Identify claim scope: algorithm vs HW implementation

**Patent target claims (rough):**
1. Method claim: adaptation via control over pretrained substrate
2. System claim: architecture with small controller + structured memory
3. HW claim: Custom SoC implementation of above (Samsung connection)

### Month 17–18: Submission

| Week | Task |
|------|------|
| M17 | Workshop paper submission (NeurIPS 2027 workshop / ICML 2027 workshop) |
| M18 | arXiv preprint public + announce on social media |
| M18 | Samsung IP formal consultation |

### Deliverables

- [ ] Paper 1 workshop version submitted
- [ ] arXiv preprint live
- [ ] Patent documents ready for IP review
- [ ] Blog post #3: "ASCENT-G: What We Found and What It Means"

---

## Phase 4: Expansion (Month 19–30)

**Goal:** Paper 1 conference version + Patent filed + Paper 2 prototype.

### Month 19–22: Paper 1 Revision + Patent

- Workshop feedback incorporation
- Additional experiments if reviewer requests
- Conference paper extension (4 pages → 8-9 pages + appendix)
- Patent: complete documents, file with Samsung IP or independently

### Month 23–26: Paper 1 Conference + Paper 2 Start

- Submit to NeurIPS 2027 / ICML 2027 (full)
- H3 experiment (self-similarity) as Paper 2 pilot
- Shared operation prototype: small-scale Universal Transformer variant
- ASCENT-A design document

### Month 27–30: Paper 2 Prototype

- Two-level controller implementation
- Comparison with ALBERT, UT, Mamba on small benchmark
- Paper 2 outline + preliminary results
- Research journey blog series

### Deliverables

- [ ] Paper 1 in conference proceedings (or under revision)
- [ ] Patent filed
- [ ] ASCENT-A prototype running
- [ ] Portfolio: 8+ blog posts, polished GitHub, possible talk

---

## Resource Planning

### Compute Budget

| Phase | Local (4090) | Cloud | Estimated Cost |
|-------|-------------|-------|----------------|
| Phase 0 | 90% | 10% | ~$30 |
| Phase 1 | 90% | 10% | ~$50 |
| Phase 2 | 60% | 40% | ~$200 |
| Phase 3 | 80% | 20% | ~$100 |
| Phase 4 | 50% | 50% | ~$400 |
| **Total** | | | **~$780** |

### Weekly Time Allocation (7.5h)

```
Weekday evenings (2h × 2 nights):  Training, monitoring, short analysis
Weekend morning/afternoon (3.5h):  Deep analysis, writing, planning
Sunday (reserved):                  Overflow / rest
```

### Compute Notes

- **GRPO on 1.5B:** Estimated 2-4h per task on RTX 4090 (batch_size=1, gradient_checkpointing=True)
- **7B experiments:** Cloud only; budget $100-200 for verification experiments
- **Vector storage:** ~10MB per task (1.5B LoRA) → 100MB for 10 tasks. No storage concern.

---

## Risk Register

| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|------------|
| H1a fails (hard) | 20% | High | Recovery path → mechanism paper |
| GRPO unstable on 1.5B | 30% | Medium | Fallback: SFT-based update vectors, test separately |
| Compute insufficient | 25% | Medium | Reduce to 5 tasks; increase cloud |
| Prior publication | 15% | High | arXiv preprint early (M8 after H1a pass) |
| Time crunch (job stress) | 40% | Medium | Pause, reduce to monthly milestone check |
| Burnout | 25% | High | Strict 7.5h cap; quarterly re-evaluation |

---

## Review Schedule

**Monthly (1st of each month, 30 min):**
- Last milestone: achieved / partial / missed
- Blocker identification
- Next month adjustment

**Quarterly (M3, M6, M9...):**
- Full timeline review
- Scenario reassessment (still Hobby track?)
- Scope adjustment if needed

**Decision gates:**
- M5: H1a pass/fail — determines Phase 2 direction
- M18: Paper 1 submit — determines Patent/Paper 2 priority
- M22: Patent decision — determines public/IP strategy

---

## Burnout Prevention Rules

1. **Hard weekly cap:** 7.5h max. If you go over regularly, reduce next week.
2. **Off-week rule:** One week per month is "review only" — no new experiments.
3. **6-month check:** Is this still worth it? Is the hypothesis still interesting? Reset if needed.
4. **Results ≠ worth:** Negative results are publishable. Inconclusive is fine.
5. **Job first:** If work is overwhelming, pause — don't "push through."
