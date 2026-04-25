# Evidence Mapping: Prior Work Supporting ASCENT

This document maps existing research to specific claims in the ASCENT framework.  
It forms the backbone of Section 2 (Related Work) in ASCENT-G (Paper 1).

Last updated: 2026-04-25

---

## Overview Table

| Prior Work | Year | What It Establishes | ASCENT Element |
|-----------|------|--------------------|----------------|
| TinyLoRA | 2026 | Small `B_adaptive` for reasoning | Central claim |
| Universal Transformer | 2018 | Shared `F_theta` is feasible | Supporting (Section 2.4) |
| ALBERT | 2019 | Cross-layer sharing works | Supporting (Section 2.4) |
| Mamba / S4 | 2023 | Selective SSM time dynamics | Supporting (Section 2.7) |
| Anthropic SAE | 2024+ | `S^base` has sparse structure | Supporting (H1c) |
| Ansuini et al. | 2019 | Low intrinsic dim of representations | Supporting (H1a) |
| Scaling Laws (Kaplan) | 2020 | Self-similar behavior across scales | Supporting (H3) |
| LoRA | 2021 | Low-rank updates sufficient | Motivating |
| SmoothQuant | 2022 | Activation-weight joint scaling | Contrast |
| GPTQ | 2022 | Hessian-guided weight quantization | Contrast |
| QuaRot / SpinQuant | 2024 | Rotation-based quantization | Contrast + motivating |
| TurboQuant | 2026 | Near-optimal KV compression | Contrast |
| SpectralQuant | 2026 | Data-aware spectral rotation | Contrast + closest prior |
| BitNet | 2023 | Native low-bit training feasible | Framing support |
| Bonsai | 2026 | Native 1-bit 8B-scale capability | Framing support |

---

## Detailed Entries

### TinyLoRA (Morris et al., 2026)

**Citation:** Morris, J. X., Mireshghallah, N., Ibrahim, M., & Mahloujifar, S. (2026). *Learning to Reason in 13 Parameters*. arXiv:2602.04118.

**Key findings:**
- 13 trained parameters (26 bytes) -> 91% GSM8K with Qwen2.5-7B
- 120 parameters -> 95% GSM8K
- SFT requires 100-1000x more parameters for equivalent performance
- Pattern holds on AIME, AMC, MATH500

**ASCENT connection:**
- **Primary evidence for central claim**
- 13 parameters ~= proxy for `B_adaptive[T_reasoning]`
- RL vs SFT asymmetry -> RL accesses existing capabilities; SFT injects new information
- Supports: substrate exists, is addressable, `B_adaptive` is small

**Open questions this raises for ASCENT-G:**
- What is the geometry of those 13 parameters?
- Do they point in a consistent direction across tasks?
- Is the space they span interpretable?

---

### Universal Transformer (Dehghani et al., 2018)

**Citation:** Dehghani, M., Gouws, S., Vinyals, O., Uszkoreit, J., & Kaiser, L. (2018). *Universal Transformers*. arXiv:1807.03819.

**Key finding:**
- Sharing parameters across depth (recurrent-style) is competitive with standard Transformers on many tasks
- Adaptive computation time: different tokens can "use" different numbers of layers

**ASCENT connection:**
- Evidence that `F_theta` (shared core operation) is feasible architecturally
- Supports operation-level self-similarity hypothesis
- Motivates the depth-axis equation: `z^(l+1, t) = F_theta(z^(l, t); gamma_l, c^slow)`

**Limitations noted:**
- Does not always outperform a full Transformer; layer-specific information may still matter
- Supports operation sharing, not representation sharing, which is consistent with ASCENT's separation

---

### ALBERT (Lan et al., 2019)

**Citation:** Lan, Z., Chen, M., Goodman, S., Gimpel, K., Sharma, P., & Soricut, R. (2019). *ALBERT: A Lite BERT for Self-Supervised Learning of Language Representations*. arXiv:1909.11942.

**Key finding:**
- Cross-layer parameter sharing (all layers share the same weights) yields 12-18x fewer parameters than BERT
- Performance remains competitive on GLUE, SQuAD, and RACE with a much smaller model

**ASCENT connection:**
- Strong practical evidence for cross-layer sharing in language models
- Supports the existence of a shared `F_theta` in `S^base`
- Shows that hierarchical representation can still develop with shared operations, consistent with ASCENT's operation/representation split

---

### Mamba / S4 (Gu, Goel, Re; Gu & Dao, 2023)

**Citations:**
- Gu, A., Goel, K., & Re, C. (2021). *Efficiently Modeling Long Sequences with Structured State Spaces*. arXiv:2111.00396.
- Gu, A., & Dao, T. (2023). *Mamba: Linear-Time Sequence Modeling with Selective State Spaces*. arXiv:2312.00752.

**Key finding:**
- Selective state-space models use input-dependent state transitions
- They achieve Transformer-level performance on many tasks without attention
- They are especially efficient for long sequences

**ASCENT connection:**
- Direct implementation candidate for `G_phi` (time-axis dynamics)
- "Selective" in Mamba is close to "control" in ASCENT: the input decides what to let through
- Motivates the "small dynamics core over structured state" design philosophy
- Connection: `G_phi(z, x, m, c^fast)` ~= selective SSM with added memory and controller inputs

---

### Anthropic Sparse Autoencoder (SAE) Research (2024+)

**Citations:**
- Bricken, T., et al. (2023). *Towards Monosemanticity: Decomposing Language Models With Dictionary Learning*. Anthropic blog.
- Templeton, A., et al. (2024). *Scaling and evaluating sparse autoencoders*. Anthropic blog.

**Key finding:**
- FFN activations decompose into sparse linear combinations of interpretable features
- Features correspond to human-interpretable concepts such as "DNA sequences" or "French text"
- Superposition means more features than dimensions are encoded in overlapping form

**ASCENT connection:**
- Evidence for interpretable structure in `S^base`
- Motivates H1c (semantic alignment hypothesis)
- "Sparse capability access" maps naturally to a controller selecting a sparse subset of substrate features
- Also challenges naive "discrete library" framing, which is why ASCENT uses "substrate" instead

---

### Intrinsic Dimensionality (Ansuini et al., 2019)

**Citation:** Ansuini, A., Laio, A., Macke, J. H., & Zoccolan, D. (2019). *Intrinsic dimensionality of data representations in deep neural networks*. NeurIPS 2019.

**Key finding:**
- Representations in deep networks have intrinsic dimension far below ambient dimension
- Intrinsic dimension varies by layer: rising early, peaking mid-network, then decreasing
- The paper provides the TwoNN estimator

**ASCENT connection:**
- Empirical support for the manifold hypothesis applied to learned representations
- Motivates H1a: if representations are low-dimensional, update directions may be as well
- Layer-dependent intrinsic dimension supports hierarchical modulation `gamma_l`
- Provides a concrete measurement method for the exploratory H5-style analysis

---

### Scaling Laws (Kaplan et al., 2020)

**Citation:** Kaplan, J., McCandlish, S., Henighan, T., Brown, T. B., Chess, B., Child, R., ... & Amodei, D. (2020). *Scaling Laws for Neural Language Models*. arXiv:2001.08361.

**Key finding:**
- Loss follows power laws in model size, dataset size, and compute
- The same equations hold across many orders of magnitude

**ASCENT connection:**
- Power-law behavior suggests scale-free, self-similar structure
- Motivates H3 (statistical self-similarity of layer weights)
- If the model obeys self-similar dynamics globally, internal structure may reflect that

---

### LoRA (Hu et al., 2021)

**Citation:** Hu, E. J., Shen, Y., Wallis, P., Allen-Zhu, Z., Li, Y., Wang, S., ... & Chen, W. (2021). *LoRA: Low-Rank Adaptation of Large Language Models*. arXiv:2106.09685.

**Key finding:**
- Rank-8 to rank-64 adapters are often sufficient for competitive fine-tuning
- Update matrices are approximated as `Delta W ~= A B` with low rank

**ASCENT connection:**
- Motivating observation: adaptation is already low-rank in practice
- TinyLoRA extends this toward the extreme: rank-1 to effectively "sub-rank-1" behavior
- LoRA success implies a low-dimensional update subspace exists; H1a asks how low it really is

---

### SmoothQuant (Xiao et al., 2022)

**Citation:** Xiao, G., Lin, J., Seznec, M., Demouth, J., & Han, S. (2022). *SmoothQuant: Accurate and Efficient Post-Training Quantization for Large Language Models*. arXiv:2211.10438.

**ASCENT contrast:**
- SmoothQuant uses joint activation-weight scaling to suppress outliers
- ASCENT reframes outliers as coordinate artifacts and asks why they appear
- SmoothQuant therefore addresses symptoms, while ASCENT aims at the geometry behind them

---

### GPTQ (Frantar et al., 2022)

**Citation:** Frantar, E., Ashkboos, S., Hoefler, T., & Alistarh, D. (2022). *GPTQ: Accurate Post-Training Quantization for Generative Pre-trained Transformers*. arXiv:2210.17323.

**ASCENT contrast:**
- GPTQ uses Hessian-guided weight quantization to preserve accuracy under compression
- It treats the existing coordinate system as fixed and optimizes local distortion within that basis
- ASCENT instead asks whether the basis itself is misaligned with the meaningful adaptation manifold

---

### QuaRot / SpinQuant (2024)

**Citations:**
- Ashkboos, S., et al. (2024). *QuaRot: Outlier-Free 4-Bit Inference in Rotated LLMs*. arXiv:2404.00456.
- Liu, Z., et al. (2024). *SpinQuant: LLM Quantization with Learned Rotations*. arXiv:2405.16406.

**ASCENT contrast + connection:**
- These works show that rotation-based quantization works by flattening problematic coordinates
- ASCENT reinterprets this as removal of coordinate artifacts without full manifold alignment
- SpinQuant's learned rotation is the closest existing move toward ASCENT's alignment idea, but without substrate framing
- On the ASCENT view, SpinQuant is partial manifold alignment: better than random rotation, but not full task-aware alignment

---

### SpectralQuant (Dynamis Labs, 2026) - Closest Prior Art

**Citation:** SpectralQuant blog, Dynamis Labs, 2026. github.com/Dynamis-Labs/spectralquant

**Key finding:**
- Uses data-aware spectral rotation with roughly 15 seconds of calibration
- Reported to exceed TurboQuant's proved near-optimal bound by 18.6% (5.95x vs 5.02x compression)

**ASCENT contrast:**
- SpectralQuant is about data-aware rotation for KV-cache compression
- ASCENT is about substrate control for task adaptation
- SpectralQuant partially validates ASCENT's "data-aware alignment" intuition
- Patent risk remains: ASCENT must differentiate itself through substrate framing and adaptation geometry, not merely "data-aware rotation"

---

### TurboQuant (Google / Zandieh et al., 2026)

**Citation:** Zandieh, A., Daliri, M., Hadian, M., & Mirrokni, V. (2026). *TurboQuant: Online Vector Quantization with Near-optimal Distortion Rate*. arXiv:2504.19874. ICLR 2026.

**Key finding:**
- Uses random rotation plus Lloyd-Max scalar quantization
- Achieves near-optimal distortion within 2.7x of the Shannon lower bound
- Focuses on data-oblivious online KV-cache compression

**ASCENT contrast:**
- TurboQuant shows flattening can be close to optimal within the data-oblivious class
- ASCENT's claim is that structured alignment can outperform flattening when geometry matters
- SpectralQuant already suggests that data-aware alignment can beat the data-oblivious bound

---

### BitNet (Wang et al., 2023) and Bonsai (Prism ML, 2026)

**Citations:**
- Wang, H., Ma, S., Dong, L., Huang, S., Wang, H., Ma, L., Yang, F., Wang, R., Wu, Y., & Wei, F. (2023). *BitNet: Scaling 1-bit Transformers for Large Language Models*. arXiv:2310.11453.
- Prism ML (2026). *1-bit Bonsai 8B: End-to-End 1-bit Language Model Deployment Across Apple, GPU, and Mobile Runtimes*. Technical Report, March 2026. https://prismml.com/news/bonsai-8b

**Key findings:**
- BitNet introduces native 1-bit transformer training and shows strong performance at scale
- BitNet b1.58 uses ternary weights `{-1, 0, +1}` and reports parity with FP16 at 3B scale
- Bonsai 8B reports native 1-bit training across all major model components
- Bonsai 8B fits 8.2B parameters into 1.15 GB and reports competitive performance versus FP16 8B models

**Critical distinction:**
- This is **not** post-training quantization
- BitNet and Bonsai train natively at low precision from scratch
- They therefore support a different claim than GPTQ, AWQ, or other PTQ methods

**ASCENT connection (framing support only):**
- Native 1-bit training suggests capability can survive under extreme value precision constraints
- That is consistent with ASCENT's framing of the pretrained model as a structured capability substrate
- This supports a structure-over-precision view of model function

**What this does not support:**
- It does **not** directly support H1a (shared adaptive subspace)
- It does **not** imply the weight manifold is low-dimensional
- It does **not** show that control dominates substrate in adaptation

**Paper 1 usage guidance:**
- Cite as framing support only, likely in 2-3 sentences
- Do not use as evidence for H1a, H1b, or H2
- Do not conflate low-precision weight storage with low-dimensional update geometry

**Deferred implication for later papers:**
- If substrate is representation-agnostic, the same capability field may appear in FP16, ternary, or binary realizations
- That idea belongs in Paper 2 or later, after Paper 1 establishes the geometric claim

**Hardware angle (deferred):**
- BitNet/Bonsai suggest binary or ternary matmul kernels
- ASCENT additionally suggests sparse substrate activation plus small control signals
- Together they motivate hybrid-precision accelerator designs, but that is outside Paper 1

---

## Gaps in Prior Literature (What ASCENT Adds)

The following are not addressed by any prior work listed above, which motivates ASCENT-G:

1. **Adaptation as control:** No prior work explicitly frames task adaptation as a control problem over substrate. TinyLoRA hints at this but does not test it geometrically.
2. **Update vector geometry across tasks:** No systematic study asks whether LoRA/TinyLoRA updates for diverse tasks share a common low-dimensional subspace.
3. **Transfer of adaptation geometry:** No direct study tests whether task-specific update directions transfer across related model variants.
4. **Information-theoretic decomposition of adaptation cost:** The `B_shared / B_adaptive` decomposition is new.
5. **Adaptation geometry linked to substrate structure:** The explicit bridge from H1a-style geometric findings to architectural implications is novel.

---

## Notes for Paper Writing

- Section 2.1 (Outlier-aware quantization): SmoothQuant, GPTQ
- Section 2.2 (Rotation-based quantization): QuaRot, SpinQuant, TurboQuant, SpectralQuant
- Section 2.3 (Online KV compression): TurboQuant, QJL, PolarQuant
- Section 2.4 (Parameter-efficient fine-tuning): LoRA, TinyLoRA, ALBERT, Universal Transformer
- Section 2.5 (Mechanistic interpretability): Anthropic SAE papers
- Section 2.6 (Representation geometry): Ansuini et al., scaling laws
- Section 2.7 (SSM / alternative architectures): Mamba, S4
- Section 2.8 (Native low-bit training, framing only): BitNet, Bonsai

**Important structural note:** BitNet and Bonsai should be cited as framing support in Related Work, not as evidence for any experimental hypothesis. They align with ASCENT's "substrate as structure" framing but answer a different question: weight precision, not update geometry.
