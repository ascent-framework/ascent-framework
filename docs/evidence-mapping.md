# Evidence Mapping: Prior Work Supporting ASCENT

This document maps existing research to specific claims in the ASCENT framework.  
It forms the backbone of §2 (Related Work) in ASCENT-G (Paper 1).

Last updated: 2026-04-18

---

## Overview Table

| Prior Work | Year | What It Establishes | ASCENT Element |
|-----------|------|--------------------|--------------------|
| TinyLoRA | 2026 | Small `B_adaptive` for reasoning | Central claim |
| Universal Transformer | 2018 | Shared `F_θ` is feasible | Supporting (§2.4) |
| ALBERT | 2019 | Cross-layer sharing works | Supporting (§2.4) |
| Mamba / S4 | 2023 | Selective SSM time dynamics | Supporting (§2.4) |
| Anthropic SAE | 2024+ | `S^base` has sparse structure | Supporting (H1c) |
| Ansuini et al. | 2019 | Low intrinsic dim of representations | Supporting (H1a) |
| Scaling Laws (Kaplan) | 2020 | Self-similar behavior across scales | Supporting (H3) |
| LoRA | 2021 | Low-rank updates sufficient | Motivating |
| SmoothQuant | 2022 | Activation-weight joint scaling | Contrast |
| GPTQ | 2022 | Hessian-guided weight quantization | Contrast |
| QuaRot / SpinQuant | 2024 | Rotation-based quantization | Contrast + motivating |
| TurboQuant | 2026 | Near-optimal KV compression | Contrast |
| SpectralQuant | 2026 | Data-aware spectral rotation | Contrast + closest prior |

---

## Detailed Entries

### TinyLoRA (Morris et al., 2026)

**Citation:** Morris, J. X., Mireshghallah, N., Ibrahim, M., & Mahloujifar, S. (2026). *Learning to Reason in 13 Parameters*. arXiv:2602.04118.

**Key findings:**
- 13 trained parameters (26 bytes) → 91% GSM8K with Qwen2.5-7B
- 120 parameters → 95% GSM8K
- SFT requires 100–1000× more parameters for equivalent performance
- Pattern holds on AIME, AMC, MATH500

**ASCENT connection:**
- **Primary evidence for central claim**
- 13 parameters ≈ proxy for `B_adaptive[T_reasoning]`
- RL vs SFT asymmetry → RL accesses existing capabilities; SFT injects new information
- Supports: substrate exists, is addressable, B_adaptive is small

**Open questions this raises for ASCENT-G:**
- What is the geometry of those 13 parameters?
- Do they point in a consistent direction across tasks?
- Is the space they span interpretable?

---

### Universal Transformer (Dehghani et al., 2018)

**Citation:** Dehghani, M., Gouws, S., Vinyals, O., Uszkoreit, J., & Kaiser, Ł. (2018). *Universal Transformers*. arXiv:1807.03819.

**Key finding:**
- Sharing parameters across depth (recurrent-style) competitive with standard Transformers on many tasks
- Adaptive computation time: different tokens can "use" different numbers of layers

**ASCENT connection:**
- Evidence that `F_θ` (shared core operation) is feasible architecturally
- Supports operation-level self-similarity hypothesis
- Motivates the depth-axis equation: `z^(ℓ+1, t) = F_θ(z^(ℓ,t); γ_ℓ, c^slow)`

**Limitations noted:**
- Doesn't always outperform full Transformer (layer-specific information may be needed)
- Supports operation sharing, not representation sharing — consistent with ASCENT's separation

---

### ALBERT (Lan et al., 2019)

**Citation:** Lan, Z., Chen, M., Goodman, S., Gimpel, K., Sharma, P., & Soricut, R. (2019). *ALBERT: A Lite BERT for Self-Supervised Learning of Language Representations*. arXiv:1909.11942.

**Key finding:**
- Cross-layer parameter sharing (all layers share same weights) → 12-18× fewer params than BERT
- Performance competitive on GLUE, SQuAD, RACE with much smaller model

**ASCENT connection:**
- Strongest practical evidence for cross-layer sharing in language models
- Supports existence of shared `F_θ` in `S^base`
- Shows that hierarchical representation develops even with shared operations (consistent with operation-level self-similarity + representation-level hierarchy separation)

---

### Mamba / S4 (Gu, Goel, Ré; Gu & Dao, 2023)

**Citations:**
- Gu, A., Goel, K., & Ré, C. (2021). *Efficiently Modeling Long Sequences with Structured State Spaces*. arXiv:2111.00396.
- Gu, A., & Dao, T. (2023). *Mamba: Linear-Time Sequence Modeling with Selective State Spaces*. arXiv:2312.00752.

**Key finding:**
- Selective state space: input-dependent state transitions
- Achieves Transformer-level performance on many tasks without attention
- Very efficient for long sequences

**ASCENT connection:**
- Direct implementation candidate for `G_φ` (time-axis dynamics)
- "Selective" in Mamba ≈ "control" in ASCENT: input decides what to let through
- Motivates "small dynamics core over structured state" design philosophy
- Connection: `G_φ(z, x, m, c^fast)` ≈ selective SSM with additional memory and controller inputs

---

### Anthropic Sparse Autoencoder (SAE) Research (2024+)

**Citations:**
- Bricken, T., et al. (2023). *Towards Monosemanticity: Decomposing Language Models With Dictionary Learning*. Anthropic blog.
- Templeton, A., et al. (2024). *Scaling and evaluating sparse autoencoders*. Anthropic blog.

**Key finding:**
- FFN activations decompose into sparse linear combinations of interpretable features
- Features correspond to human-interpretable concepts (e.g., "DNA sequences", "French text")
- Superposition: more features than dimensions, overlapping encoding

**ASCENT connection:**
- Evidence for interpretable structure in `S^base`
- Motivates H1c (semantic alignment hypothesis)
- "Sparse capability access" ≈ controller selects sparse subset of substrate features
- Challenge to ASCENT: superposition makes "discrete library" framing wrong → motivates "substrate" framing instead of "library"

---

### Intrinsic Dimensionality (Ansuini et al., 2019)

**Citation:** Ansuini, A., Laio, A., Macke, J. H., & Zoccolan, D. (2019). *Intrinsic dimensionality of data representations in deep neural networks*. NeurIPS 2019.

**Key finding:**
- Representations in deep networks have intrinsic dimension much lower than ambient dimension
- Intrinsic dimension varies by layer: rises in early layers, peaks in middle, decreases in final layers
- TwoNN algorithm for estimation

**ASCENT connection:**
- Empirical support for manifold hypothesis applied to representations
- Motivates H1a: if representations are low-dimensional, update directions may be too
- Layer-dependent intrinsic dim supports hierarchical modulation `γ_ℓ`
- Provides measurement methodology for our exploratory H5 analysis

---

### Scaling Laws (Kaplan et al., 2020)

**Citation:** Kaplan, J., McCandlish, S., Henighan, T., Brown, T. B., Chess, B., Child, R., ... & Amodei, D. (2020). *Scaling Laws for Neural Language Models*. arXiv:2001.08361.

**Key finding:**
- Loss follows power laws in model size, dataset size, compute
- Scale-free behavior: same equations hold across many orders of magnitude

**ASCENT connection:**
- Power-law behavior ≈ scale-free / self-similar structure
- Motivates H3 (statistical self-similarity of layer weights)
- If the model obeys self-similar dynamics globally, internal structure may reflect this

---

### LoRA (Hu et al., 2021)

**Citation:** Hu, E. J., Shen, Y., Wallis, P., Allen-Zhu, Z., Li, Y., Wang, S., ... & Chen, W. (2021). *LoRA: Low-Rank Adaptation of Large Language Models*. arXiv:2106.09685.

**Key finding:**
- Rank-8 to rank-64 adapters sufficient for competitive fine-tuning performance
- Update matrix `ΔW ≈ AB` where A, B are low-rank

**ASCENT connection:**
- Motivating observation: adaptation is inherently low-rank
- TinyLoRA extends this to extreme: rank-1 to "sub-rank-1" (< model dimension)
- LoRA success implies low-dimensional update subspace exists — H1a is testing how low

---

### SmoothQuant (Xiao et al., 2022)

**Citation:** Xiao, G., Lin, J., Seznec, M., Demouth, J., & Han, S. (2022). *SmoothQuant: Accurate and Efficient Post-Training Quantization for Large Language Models*. arXiv:2211.10438.

**ASCENT contrast:**
- SmoothQuant: joint activation-weight scaling to handle outliers
- ASCENT: outliers are coordinate artifacts; reframe as coordinate selection problem
- Difference: SmoothQuant addresses symptoms; ASCENT asks why outliers exist

---

### QuaRot / SpinQuant (2024)

**Citations:**
- Ashkboos, S., et al. (2024). *QuaRot: Outlier-Free 4-Bit Inference in Rotated LLMs*. arXiv:2404.00456.
- Liu, Z., et al. (2024). *SpinQuant: LLM Quantization with Learned Rotations*. arXiv:2405.16406.

**ASCENT contrast + connection:**
- These show rotation-based quantization works (flattening effect)
- ASCENT reinterprets: rotation removes coordinate artifacts but doesn't align to manifold
- SpinQuant's learned rotation is closest to ASCENT's alignment idea — but without substrate framing
- SpinQuant is partial manifold alignment: better than random, worse than full alignment

---

### SpectralQuant (Dynamis Labs, 2026) ⚠️ CLOSEST PRIOR ART

**Citation:** SpectralQuant blog, Dynamis Labs, 2026. github.com/Dynamis-Labs/spectralquant

**Key finding:**
- Data-aware spectral rotation (15-second calibration)
- Exceeds TurboQuant's proved near-optimal bound by 18.6% (5.95× vs 5.02× compression)

**ASCENT contrast:**
- SpectralQuant: data-aware rotation for KV cache compression
- ASCENT: substrate control for task adaptation
- Domain: SpectralQuant = KV cache; ASCENT = weight adaptation
- SpectralQuant partially validates ASCENT's "data-aware alignment" intuition
- **Patent risk:** SpectralQuant covers data-aware spectral rotation; ASCENT must differentiate on substrate framing, not just data-aware rotation

---

### TurboQuant (Google/Zandieh et al., 2026)

**Citation:** Zandieh, A., Daliri, M., Hadian, M., & Mirrokni, V. (2026). *TurboQuant: Online Vector Quantization with Near-optimal Distortion Rate*. arXiv:2504.19874. ICLR 2026.

**Key finding:**
- Random rotation + Lloyd-Max scalar quantization
- Near-optimal distortion: within 2.7× of Shannon lower bound
- Data-oblivious, online, KV cache focused

**ASCENT contrast:**
- TurboQuant: random rotation (flattening) → optimal within data-oblivious class
- ASCENT: structured rotation (alignment) → beyond data-oblivious bound
- TurboQuant proves flattening is not enough for ultimate efficiency
- SpectralQuant already beat TurboQuant using data-aware alignment

---

## Gaps in Prior Literature (What ASCENT Adds)

The following are **not addressed** by any prior work listed above, which motivates ASCENT-G:

1. **Adaptation = control framing:** No prior work explicitly frames task adaptation as a control problem over substrate. TinyLoRA authors suggest it ("activating capabilities") but don't test it geometrically.

2. **Update vector geometry across tasks:** No systematic study of whether LoRA/TinyLoRA updates share a low-dimensional subspace across diverse tasks.

3. **Transfer of adaptation geometry:** No study of whether task-specific update directions transfer across model variants.

4. **Information-theoretic decomposition of adaptation cost:** The B_shared/B_adaptive decomposition is new.

5. **Connection between adaptation geometry and substrate structure:** The explicit link between H1a (subspace) and architectural implications (ASCENT-A) is novel.

---

## Notes for Paper Writing

- §2.1 (Outlier-aware quantization): SmoothQuant, GPTQ
- §2.2 (Rotation-based quantization): QuaRot, SpinQuant, TurboQuant, SpectralQuant
- §2.3 (Online KV compression): TurboQuant, QJL, PolarQuant
- §2.4 (Parameter-efficient fine-tuning): LoRA, TinyLoRA, ALBERT, UT
- §2.5 (Mechanistic interpretability): SAE papers (Anthropic)
- §2.6 (Representation geometry): Ansuini et al., scaling laws
- §2.7 (SSM / alternative architectures): Mamba, S4
