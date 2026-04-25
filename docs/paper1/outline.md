# ASCENT-G: Paper 1 Outline

**Working Title:** Adaptation Geometry in Pretrained Language Models  
**Subtitle:** Do Task-Specific Updates Reveal a Shared Capability Substrate?  
**Target:** NeurIPS 2027 Workshop or NeurIPS/ICML/ICLR 2027 (main)  
**Format:** 8 pages + appendix  
Status: Outline stage (experiments not yet run)

---

## Abstract (draft placeholder)

Recent work shows that task adaptation via reinforcement learning can achieve strong performance with as few as 13 trainable parameters (TinyLoRA). This extreme efficiency suggests that RL-based adaptation may primarily *activate* existing model capabilities rather than inject new knowledge. We investigate this hypothesis by analyzing the geometry of task-specific update vectors in pretrained LLMs. Specifically, we ask: do updates for diverse tasks share a common low-dimensional subspace, and if so, do task directions remain separable within it? We pre-register three primary hypotheses (H1a: shared subspace; H1b: directional separability; H2: cross-model transfer) and provide a falsification ladder that maps experimental outcomes to specific allowed claims. Our results [TBD]. These findings [TBD] suggest that task adaptation cost is dominated by control bits rather than injection bits, motivating new architectural principles for efficient language models.

---

## Section 1. Introduction (~1.5 pages)

**Goal:** Motivate the question, state the contribution, preview results.

**Structure:**
1. The TinyLoRA paradox: why does 13-parameter RL adaptation work when 13-parameter SFT does not?
2. Optional opening framing: reducibility analogies such as NAND or EML as rhetoric only, not as evidence.
3. Two interpretations:
   - Standard: RL is more information-efficient per parameter.
   - ASCENT: RL accesses existing capabilities; SFT tries to inject new ones.
4. This interpretation predicts a testable geometric property: updates should share a structured subspace.
5. We test this prediction with pre-registered experiments.
6. Summary of findings [TBD].
7. Contribution statement:
   - First geometric analysis of TinyLoRA update vectors.
   - Pre-registered falsification framework.
   - `B_shared / B_adaptive` decomposition.
   - Implications for architecture as a forward pointer, not a tested Paper 1 claim.

**Key framing sentence (draft):**
> "We reframe task adaptation as a control problem over a pretrained capability substrate, and ask whether the geometry of update vectors provides evidence for this view."

---

## Section 2. Related Work (~1 page)

**Structure:**
- Section 2.1 Outlier-aware and post-training quantization (SmoothQuant, GPTQ)
- Section 2.2 Rotation-based quantization and KV compression (QuaRot, SpinQuant, TurboQuant, SpectralQuant)
- Section 2.3 Parameter-efficient fine-tuning and operation sharing (LoRA, TinyLoRA, ALBERT, Universal Transformer)
- Section 2.4 Mechanistic interpretability and capability discovery (SAE)
- Section 2.5 Representation geometry and scale behavior (Ansuini et al., Kaplan et al.)
- Section 2.6 SSM / alternative architectures (Mamba, S4)
- Section 2.7 Native low-bit training, framing only (BitNet, Bonsai)

Source: expand from `docs/paper1/evidence-mapping.md`

**Scope note:**
- EML, if mentioned at all, appears only as a brief structural-reducibility analogy in the introduction or a single sentence in related work, not as technical evidence.

**Key paragraph:**
> "Unlike prior work that analyzes geometry for compression, we analyze geometry as evidence for a functional hypothesis about adaptation. And unlike interpretability work that decomposes representations, we decompose update directions as a complementary lens."

---

## Section 3. Framework (~1 page)

**Subsections:**
- Section 3.1 Central claim: adaptation as control
- Section 3.2 Information decomposition: `B_model[T] = B_shared[T] + B_adaptive[T]`
- Section 3.3 Substrate hierarchy: `S = {S^fast, S^slow, S^base, S^meta}`
- Section 3.4 Predictions: if the claim is true, what geometry should we see?

Source: condense from `docs/framework/v6.1.md`

---

## Section 4. Methodology (~1.5 pages)

**Subsections:**
- Section 4.1 Pre-registration statement (link to OSF)
- Section 4.2 Models: Qwen2.5-1.5B-Instruct
- Section 4.3 Task distribution: `T_reasoning` (10 tasks)
- Section 4.4 Update vector extraction (LoRA adapter concat)
- Section 4.5 Normalization (direction vs. magnitude, separate)
- Section 4.6 Training protocol (GRPO, 1000 steps)
- Section 4.7 Analysis pipeline (SVD, bootstrap CI, projection)

Source: condense from `docs/preregistration/v1.3.md`

---

## Section 5. Experiments (~2 pages)

**Section 5.1 H1a: Shared Adaptive Subspace**
- Main table: `rho` values per task family
- Figure 1: Singular value spectrum (how quickly does energy decay?)
- Figure 2: Bootstrap CI for `rho`
- Result: [TBD: strong pass / marginal / inconclusive / fail]

**Section 5.2 H1b: Directional Separability**
- Figure 3: Pairwise cosine similarity heatmap (10x10 task matrix)
- Figure 4: Distribution of `|cos|` values
- Result: [TBD]

**Section 5.3 H2: Transfer**
- Table 2: Retention scores across model pairs
- Result: [TBD]

**Section 5.4 Claim tier determination**
- Based on Section 7 interpretation rules from the pre-registration
- Clearly state which tier was reached

---

## Section 6. Analysis (~1 page)

**Exploratory analyses (clearly labeled):**
- Section 6.1 H1c: Semantic alignment (if SAE features are available)
- Section 6.2 `B_adaptive` magnitude trends: does harder task imply larger `||u_i||`?
- Section 6.3 Intrinsic dimension estimate (TwoNN)
- Section 6.4 H3: Layer self-similarity (Wasserstein distances)
- Section 6.5 Optional basin-of-attraction recovery check (non-registered; only if time permits)

**Figure 5:** Magnitude `||u_i||` vs. task difficulty (correlation plot)

---

## Section 7. Discussion (~0.5 pages)

**Points:**
1. What the geometry tells us about substrate structure.
2. RL vs. SFT asymmetry through the substrate lens.
3. Connection to Universal Transformer / ALBERT and the broader operation-sharing literature.
4. Limitations: 1.5B model, `T_reasoning` only, no causal claims.

**Key paragraph:**
> "If H1a and H1b are confirmed, the substrate framing predicts that efficient architectures should separate the 'control' component (small, task-sensitive) from the 'knowledge' component (large, task-shared). This motivates the design principle explored in ASCENT-A."

---

## Section 8. Conclusion (~0.25 pages)

- One-sentence summary of finding
- Implication for adaptation research
- Implication for architecture design (forward pointer)
- Pointer to code and OSF

---

## Appendix

- A: Full pre-registration document (v1.3)
- B: Hyperparameter sensitivity analysis
- C: Per-task training curves (loss, reward)
- D: Extended H1c results (if available)
- E: Reproducibility checklist

---

## Figures List (target: 5 main + appendix)

| Figure | Content | Section |
|--------|---------|---------|
| Fig. 1 | Singular value spectrum (energy decay) | Section 5.1 |
| Fig. 2 | Bootstrap CI for `rho` by task family | Section 5.1 |
| Fig. 3 | Pairwise cosine similarity heatmap | Section 5.2 |
| Fig. 4 | `|cos|` distribution histogram | Section 5.2 |
| Fig. 5 | Task difficulty vs. `||u_i||` | Section 6.2 |
| App. Fig. 1 | Per-task training curves | Appendix C |
| App. Fig. 2 | Intrinsic dimension estimates | Appendix D |

---

## Tables List

| Table | Content | Section |
|-------|---------|---------|
| Table 1 | H1a results: `rho`, CI, tier | Section 5.1 |
| Table 2 | H2 transfer: Retention % | Section 5.3 |
| Table 3 | Task metadata (N, domain, difficulty) | Section 4.3 |

---

## Writing Timeline

| Month | Target |
|-------|--------|
| M11 | Section 3, Section 4 first draft (from existing docs) |
| M12 | Section 5, Section 6 first draft (from experiment results) |
| M13 | Section 1, Section 2, Section 7, Section 8 first draft |
| M14 | Full draft v1 complete |
| M15 | External feedback round |
| M16 | Revision (draft v2) |
| M17 | Workshop submission |
| M18 | arXiv preprint |

---

## Key Decisions Still Open

- [ ] Exact venue target (which workshop?)
- [ ] Whether to include 7B experiments (requires cloud)
- [x] SAE availability for H1c (Gemma Scope SAE available via `google/gemma-2-2b-it`)
- [ ] Author list (solo or with collaborator?)
- [ ] License for code/data release
