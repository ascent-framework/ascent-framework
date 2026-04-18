# ASCENT-G: Paper 1 Outline

**Working Title:** Adaptation Geometry in Pretrained Language Models  
**Subtitle:** Do Task-Specific Updates Reveal a Shared Capability Substrate?  
**Target:** NeurIPS 2027 Workshop → NeurIPS/ICML/ICLR 2027 (main)  
**Format:** 8 pages + appendix  
Status: Outline stage (experiments not yet run)

---

## Abstract (draft placeholder)

Recent work shows that task adaptation via reinforcement learning can achieve strong performance with as few as 13 trainable parameters (TinyLoRA). This extreme efficiency suggests that RL-based adaptation may primarily *activate* existing model capabilities rather than inject new knowledge. We investigate this hypothesis by analyzing the geometry of task-specific update vectors in pretrained LLMs. Specifically, we ask: do updates for diverse tasks share a common low-dimensional subspace, and if so, do task directions remain separable within it? We pre-register three primary hypotheses (H1a: shared subspace; H1b: directional separability; H2: cross-model transfer) and provide a falsification ladder that maps experimental outcomes to specific allowed claims. Our results [TBD]. These findings [TBD] and suggest that task adaptation cost is dominated by control bits rather than injection bits, motivating new architectural principles for efficient language models.

---

## §1. Introduction (~1.5 pages)

**Goal:** Motivate the question, state the contribution, preview results.

**Structure:**
1. The TinyLoRA paradox: why does 13-parameter RL adaptation work when 13-parameter SFT doesn't?
2. Two interpretations:
   - Standard: RL is more information-efficient per parameter
   - ASCENT: RL accesses existing capabilities; SFT tries to inject new ones
3. This interpretation predicts a testable geometric property: updates should share a structured subspace
4. We test this prediction with pre-registered experiments
5. Summary of findings [TBD]
6. Contribution statement:
   - First geometric analysis of TinyLoRA update vectors
   - Pre-registered falsification framework
   - B_shared/B_adaptive decomposition
   - Implications for architecture (pointer to ASCENT-A)

**Key framing sentence (draft):**
> "We reframe task adaptation as a control problem over a pretrained capability substrate, and ask whether the geometry of update vectors provides evidence for this view."

---

## §2. Related Work (~1 page)

**Structure:**
- §2.1 Parameter-efficient fine-tuning (LoRA, TinyLoRA, LoRA-XS)
- §2.2 Mechanistic interpretability and capability discovery (SAE, circuits)
- §2.3 Intrinsic dimensionality of representations (Ansuini et al.)
- §2.4 Architecture sharing (Universal Transformer, ALBERT)
- §2.5 Rotation-based quantization (QuaRot, SpinQuant, TurboQuant) — contrast
- §2.6 Coordinate geometry for LLM compression (operator-coordinate-compression)

Source: expand from `docs/evidence-mapping.md`

**Key paragraph:**
> "Unlike prior work that analyzes geometry for compression, we analyze geometry as evidence for a functional hypothesis about adaptation. And unlike interpretability work that decomposes representations, we decompose update directions — a complementary lens."

---

## §3. Framework (~1 page)

**Subsections:**
- §3.1 Central claim: adaptation as control
- §3.2 Information decomposition: B_model[T] = B_shared[T] + B_adaptive[T]
- §3.3 Substrate hierarchy: S = {S^fast, S^slow, S^base, S^meta}
- §3.4 Predictions: if claim is true, what geometry should we see?

Source: condense from `docs/framework/v6.1.md`

---

## §4. Methodology (~1.5 pages)

**Subsections:**
- §4.1 Pre-registration statement (link to OSF)
- §4.2 Models: Qwen2.5-1.5B-Instruct
- §4.3 Task distribution: T_reasoning (10 tasks)
- §4.4 Update vector extraction (LoRA adapter concat)
- §4.5 Normalization (direction vs magnitude, separate)
- §4.6 Training protocol (GRPO, 1000 steps)
- §4.7 Analysis pipeline (SVD, bootstrap CI, projection)

Source: condense from `docs/preregistration/v1.2.md`

---

## §5. Experiments (~2 pages)

**§5.1 H1a: Shared Adaptive Subspace**
- Main table: ρ values per task family
- Figure 1: Singular value spectrum (how quickly does energy decay?)
- Figure 2: Bootstrap CI for ρ
- Result: [TBD — strong pass / marginal / etc.]

**§5.2 H1b: Directional Separability**
- Figure 3: Pairwise cosine similarity heatmap (10×10 task matrix)
- Figure 4: Distribution of |cos| values
- Result: [TBD]

**§5.3 H2: Transfer**
- Table 2: Retention scores across model pairs
- Result: [TBD]

**§5.4 Claim tier determination**
- Based on §7 Interpretation Rules from pre-registration
- Clearly state which tier was reached

---

## §6. Analysis (~1 page)

**Exploratory analyses (clearly labeled):**
- §6.1 H1c: Semantic alignment (if SAE features available)
- §6.2 B_adaptive magnitude trends: does harder task = larger ||u_i||?
- §6.3 Intrinsic dimension estimate (TwoNN)
- §6.4 H3: Layer self-similarity (Wasserstein distances)

**Figure 5:** Magnitude ||u_i|| vs. task difficulty (correlation plot)

---

## §7. Discussion (~0.5 pages)

**Points:**
1. What the geometry tells us about substrate structure
2. RL vs SFT asymmetry through the substrate lens
3. Connection to Universal Transformer / ALBERT (operation sharing)
4. Limitations: 1.5B model, T_reasoning only, no causal claims

**Key paragraph:**
> "If H1a and H1b are confirmed, the substrate framing predicts that efficient architectures should separate the 'control' component (small, task-sensitive) from the 'knowledge' component (large, task-shared). This motivates the design principle explored in ASCENT-A."

---

## §8. Conclusion (~0.25 pages)

- One-sentence summary of finding
- Implication for adaptation research
- Implication for architecture design (forward pointer)
- Pointer to code + OSF

---

## Appendix

- A: Full pre-registration document (v1.2)
- B: Hyperparameter sensitivity analysis
- C: Per-task training curves (loss, reward)
- D: Extended H1c results (if available)
- E: Reproducibility checklist

---

## Figures List (target: 5 main + appendix)

| Figure | Content | Section |
|--------|---------|---------|
| Fig. 1 | Singular value spectrum (energy decay) | §5.1 |
| Fig. 2 | Bootstrap CI for ρ by task family | §5.1 |
| Fig. 3 | Pairwise cosine similarity heatmap | §5.2 |
| Fig. 4 | |cos| distribution histogram | §5.2 |
| Fig. 5 | Task difficulty vs ||u_i|| | §6.2 |
| App. Fig. 1 | Per-task training curves | Appendix C |
| App. Fig. 2 | Intrinsic dimension estimates | Appendix D |

---

## Tables List

| Table | Content | Section |
|-------|---------|---------|
| Table 1 | H1a results: ρ, CI, tier | §5.1 |
| Table 2 | H2 transfer: Retention % | §5.3 |
| Table 3 | Task metadata (N, domain, difficulty) | §4.3 |

---

## Writing Timeline

| Month | Target |
|-------|--------|
| M11 | §3, §4 first draft (from existing docs) |
| M12 | §5, §6 first draft (from experiment results) |
| M13 | §1, §2, §7, §8 first draft |
| M14 | Full draft v1 complete |
| M15 | External feedback round |
| M16 | Revision (draft v2) |
| M17 | Workshop submission |
| M18 | arXiv preprint |

---

## Key Decisions Still Open

- [ ] Exact venue target (which workshop?)
- [ ] Whether to include 7B experiments (requires cloud)
- [ ] SAE availability for H1c (Gemma SAE exists, Qwen SAE?)
- [ ] Author list (solo or with collaborator?)
- [ ] License for code/data release
