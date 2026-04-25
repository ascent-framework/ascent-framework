# ASCENT ↔ Looped Transformers: A Theoretical Connection

**Author Note / Working Document**  
Date: 2026-04-18  
Status: Analysis draft - may evolve into Paper 1 §7 Discussion or standalone note

---

## Executive Summary

In April 2026, two developments made looped, shared-parameter architectures newly relevant to ASCENT:

1. **Parcae** (Prairie et al., UCSD + Together AI, arXiv:2604.12946) provides an **empirical anchor**: stable looped language models with explicit training and test-time scaling laws, and strong parameter-efficiency results.

2. **OpenMythos** (kyegomez, github.com/kyegomez/OpenMythos) provides a **community-built theoretical reconstruction / prototype**: a concrete implementation of one possible "Mythos-like" recurrent-depth design that combines looping with MoE, MLA/GQA, and ACT-style halting.

These are not the same kind of evidence. Parcae is the empirical result. OpenMythos is useful primarily as a hypothesis generator and implementation playground, not as evidence about Anthropic's actual architecture.

This document maps the structural compatibilities between ASCENT and recent looped architectures, identifies what ASCENT may add as a theoretical lens, and adjusts ASCENT's research strategy while keeping the claim strength calibrated to the available evidence.

---

## 1. The Core Structural Parallel

### ASCENT v6.1 (space axis)

```
z^(ℓ+1, t) = F_θ(z^(ℓ,t); γ_ℓ, c^slow)
```

- `F_θ`: shared core operation across depth `ℓ`
- `γ_ℓ`: hierarchical per-layer modulation
- `c^slow`: task-level controller (stable across sequence)

### Looped Transformer Family

```
h_{t+1} = A·h_t + B·e + Transformer(h_t, e)
```

- `Transformer(·, ·)`: shared block applied across loop iterations `t`
- `A, B`: injection parameters controlling recurrent dynamics
- `e`: encoded input from Prelude (injected at every loop)

### Proposed Correspondences

The table below is intentionally conservative. Some rows are strong structural analogies; others are interpretive correspondences that should be treated as hypotheses rather than 1:1 equivalences.

| ASCENT Element | Looped Transformer Feature | Strength |
|---------------|----------------------------|----------|
| Shared `F_θ` across depth | Recurrent block reused across loops | Strong structural analogy |
| Depth index `ℓ` | Loop iteration `t` | Strong structural analogy |
| Hierarchical modulation `γ_ℓ` | Loop-index embedding or per-loop modulation (for example LoRA-per-loop) | Moderate, implementation-dependent |
| Input controller `c^fast_t` | Per-step input injection `B·e` | Moderate functional analogy |
| Substrate `S^base` | Shared recurrent block parameters | Strong structural analogy |
| Substrate `S^fast` | Hidden-state trajectory `h_t` | Strong structural analogy |
| Stable dynamics requirement | Spectral-radius constraint `ρ(A) < 1` in Parcae | Strong for Parcae specifically |
| Task controller `c^slow` | Task-conditioned routing / halting / depth policy | Interpretive, not strict 1:1 |

**Observation:** Parcae provides an existence proof that at least one architecture family structurally compatible with parts of ASCENT can be trained stably and scaled. OpenMythos explores a broader package of compatible design ideas, but it should be read as a conjectural reconstruction rather than as empirical evidence on par with Parcae.

---

## 2. What Each Project Contributes

### Parcae contributes (empirical)

- **Stable large-scale training recipe** for looped language models
- **Scaling laws for looping:** optimal `μ_rec ∝ C^0.40`, optimal tokens `∝ C^0.78`
- **Mechanism for stability:** spectral control via constrained injection parameters
- **Parameter-efficiency evidence:** strong quality at fixed parameter budgets, with a 770M Parcae model approaching the quality of a 1.3B Transformer
- **Test-time compute law:** `L(T) = L_∞ + Z·e^(-z·T)` (saturating exponential)

### OpenMythos contributes (hypothesis / prototype)

- **A worked reconstruction** of a possible Prelude + Recurrent + Coda looped architecture
- **A concrete implementation target** that combines MoE, MLA/GQA, ACT-style halting, and loop differentiation
- **A useful analysis sandbox** for testing ideas about recurrent-depth inference, routing, and latent reasoning
- **A broader design-space proposal** for what a "looped frontier-style model" might look like

OpenMythos is useful because it makes design ideas inspectable. It is not authoritative evidence about Claude Mythos, and it should not be cited as if it had the evidentiary status of a peer-reviewed empirical paper.

### What ASCENT may contribute if validated

Looking at both projects carefully, there are three places where ASCENT may add theoretical value.

**Gap 1: Why can shared-parameter looping work at all?**

Parcae shows that it *can* work and that it can be made stable. OpenMythos offers an architectural story about what might happen inside such loops. Neither, by itself, fully explains why one shared block can support diverse task behaviors.

**ASCENT offers one candidate account:** task-relevant behavior may live in a controllable, lower-dimensional substrate. On that view, loop iteration is less like repeatedly relearning a task and more like progressively steering computation through already available substrate structure.

**Gap 2: What is the adaptation cost of a looped model?**

Parcae provides compute scaling laws over FLOPs, loops, and tokens. Neither project directly addresses *adaptation* cost: how much additional task-specific information is required to specialize a shared-parameter looped system.

**ASCENT offers a lens here:** `B_adaptive[T] = task-specific control bits`. In a looped setting, those bits might show up as task-conditioned changes to:

- halting or depth policy
- routing behavior
- preferred recurrence budget
- small controller parameters layered on top of a shared recurrent core

This is not yet derived from Parcae's scaling law; it is a testable conjecture that ASCENT can formulate explicitly.

**Gap 3: What is the geometry of the internal substrate?**

OpenMythos describes hidden states `h_t` as evolving across loops. Parcae studies stability and scaling. Neither currently characterizes the geometry of those state updates in a way directly comparable to ASCENT's update-subspace claims.

**ASCENT makes a testable prediction:** if the substrate view is right, then `Δh_t = h_{t+1} - h_t` across tasks should:

- lie substantially in a shared low-dimensional subspace
- remain at least partly separable across tasks within that subspace
- potentially align with interpretable capability activations

This is an empirical prediction that can be tested on Parcae checkpoints and, with the appropriate caveats, on OpenMythos prototypes.

---

## 3. Mutual Implications

### What Parcae and OpenMythos suggest about ASCENT

**Implication A: ASCENT-A's architectural family is no longer unsupported speculation.**  
Parcae shows that one important member of the looped/shared-parameter family is trainable and effective. That does not validate ASCENT as a theory, but it does make the architectural direction materially more plausible.

**Implication B: The broader design space is now concrete enough to analyze.**  
Even though OpenMythos is speculative, it packages MoE, looping, routing, and halting into a concrete artifact. That is useful for Paper 2 style theoretical framing because it turns a vague design family into an inspectable object.

**Implication C: Timing is favorable.**  
If ASCENT-G (Paper 1) reaches submission in M18, it enters a literature that is actively re-examining recurrent-depth and shared-parameter architectures. A theory paper about *what internal structure makes these models reusable* would land into an active conversation rather than an empty one.

### What ASCENT may suggest about looped models

**Implication A: Test-time loop saturation may have a substrate-style interpretation.**  
Parcae reports a saturating test-time law, `L(T) = L_∞ + Z·e^(-z·T)`. ASCENT suggests one possible explanation: additional loops help until the controller has already exploited most of the task-relevant substrate, after which returns diminish. This is a plausible interpretation, not a derivation.

**Implication B: Dynamic routing can be re-read as control over shared structure.**  
If one adopts the controller/substrate distinction, then MoE routing or depth policy can be interpreted as mechanisms for selecting which parts of a shared internal substrate are engaged. This is a useful framing for Paper 2, but it should be presented as theoretical reinterpretation rather than established fact.

**Implication C: Reasoning vs. memorization tradeoffs may have structural causes.**  
A looped architecture may be biased toward composition over brute memorization because it reuses a shared core and spends additional compute on iterative control. ASCENT turns that intuition into a more explicit hypothesis using `B_shared` and `B_adaptive`, but this remains conjectural until directly tested.

---

## 4. Adjusted Research Strategy

Given this analysis, ASCENT's research strategy requires adjustment in three places.

### 4.1 Paper 1 (ASCENT-G) - Scope Extension (Optional)

**Original scope:** TinyLoRA update-vector geometry on Qwen2.5-1.5B.

**Optional extension:** Add Section 5.5, "Internal Dynamics in Looped Models," testing analogous hypotheses on hidden-state trajectories in a looped model.

**Experiment H4 (new, secondary):**

```
H4 (Looped Transformer Substrate Evidence):
  In a looped model, the per-loop state updates Δh_t
  concentrate in a low-dimensional subspace shared across tasks,
  while individual tasks traverse at least partly separable paths
  within that subspace.

Primary extraction:
  - Use one fixed prompt template family across tasks
  - Match input length where feasible
  - Extract h_t at the output of the recurrent block after each loop
  - Primary readout: final answer-token position
  - Robustness readout: mean-pooled non-padding token states

Controls:
  - Multiple prompt paraphrases per task to estimate within-task lexical variance
  - Length / template controls before comparing across tasks
  - Compare between-task separation against within-task prompt variance

Measurement:
  - Compute Δh^i_t = h^i_{t+1} - h^i_t
  - Center trajectories with respect to prompt-template controls
  - Run SVD or PCA on stacked Δh vectors across tasks
  - Test separability with task labels inside the top-k shared subspace

Pass:
  Δh trajectories show both shared low-rank structure and
  nontrivial task separability beyond prompt-level variance

Fail:
  Δh structure is dominated by prompt surface form, token position,
  or effectively random task-independent drift
```

**Decision:** Include this only if Phase 1 (TinyLoRA experiments) succeeds and compute budget allows. Do not put H4 into pre-registration v1.2. It would add scope and interpretation burden too early.

**If included**, the paper becomes stronger because it would connect parameter-update geometry and forward-pass recurrent geometry under one hypothesis. But the extra value is only worth it if the experiment is run with proper controls.

### 4.2 Paper 2 (ASCENT-A) - Repositioning

**Original plan:** Propose a "new architecture" derived from ASCENT.

**Problem:** Parcae already provides a serious empirical member of the relevant architecture family, and OpenMythos already sketches a broader speculative variant. Another "here is yet another architecture" paper is less interesting than a theory paper explaining a family.

**Repositioning:** Make Paper 2 a **theoretical framework paper** rather than a primary architecture paper.

**New working title:**  
*"Control over Substrate: A Unified Theoretical Framework for Looped, Shared-Parameter, and Modular Language Models"*

**Paper 2 scope (revised):**

- Section 3: formal statement of the ASCENT framework
- Section 4: unified description of Universal Transformer, ALBERT, Parcae, and related looped/shared-parameter models under ASCENT
- Section 5: hypotheses about efficiency tradeoffs via `B_shared / B_adaptive`
- Section 6: direct validation on selected looped models
- Section 7: implications for architecture and controller design

This positioning has three advantages:

- avoids direct competition with Parcae-style empirical architecture papers
- increases citation potential by explaining multiple model families at once
- follows naturally from Paper 1 if the geometric results are strong

**Important framing rule:** In this paper, Parcae should be treated as empirical evidence; OpenMythos should be treated as a worked reconstruction / exploratory artifact, not as validating evidence about Anthropic.

### 4.3 Paper 3 (ASCENT-D) - Enhanced Connection

**Original plan:** World-model -> language curriculum (developmental substrate).

**Enhancement:** connect it to looped training protocols, but only at the hypothesis level.

Parcae shows that mean recurrence during training matters and that train-time recurrence/data tradeoffs are structured. One plausible next question is whether the composition of pre-training data affects the *depth capacity* that a model can productively exploit at inference. That is not established by Parcae; it is a natural follow-up question.

ASCENT-D can therefore ask:

> How does pre-training data composition affect the internal substrate's later capacity for productive looped reasoning?

Example experiment: train small Parcae variants on pure language vs. language+world-model data. Test whether the world-model-trained variant exhibits a different test-time saturation profile, for example via `L_∞` or `z`. This should be framed as a hypothesis test, not as something already implied by Parcae.

---

## 5. Risk Assessment

### Risks that increased

**Risk: Claim-strength mismatch.**  
The biggest near-term risk is overclaiming from a mixed evidence base: Parcae is empirical, OpenMythos is speculative, and ASCENT is theoretical. If those are written as though they carry equal evidentiary weight, the document becomes easier to attack.

**Mitigation:**

- clearly separate empirical evidence, speculative reconstruction, and theoretical interpretation
- cite OpenMythos explicitly as a reconstruction / software artifact, not as evidence about Anthropic
- phrase ASCENT's role as "candidate explanation" and "testable prediction," not as settled explanation

**Risk: Timing competition.**  
The looped-model space is active. A theoretical framework paper from ASCENT likely has a limited window before other groups publish overlapping interpretations.

**Mitigation:**

- public preprint of framework v6.1 + this connection note as early as practical
- OSF pre-registration of Paper 1 experiments to establish priority on the geometry hypothesis
- emphasize the specific theoretical moves others are not currently making: adaptation geometry, control bits, and substrate interpretation

**Risk: Scope bloat.**  
It is tempting to fold looped-model experiments into Paper 1 prematurely.

**Mitigation:** Keep Paper 1 tightly scoped to TinyLoRA unless the optional H4 experiment is clearly additive and operationally well controlled.

### Risks that decreased

**Risk: "Framework is hypothetical."**  
Before: ASCENT described an architecture family that might have seemed abstract or impractical.  
Now: Parcae shows that at least one related family is real, trainable, and competitive.

**Risk: "What's the architectural implication?"**  
Before: ASCENT-A would have needed to motivate an architecture from scratch.  
Now: ASCENT-A can explain and organize an architecture family that already matters.

### New opportunity

**Collaboration potential.** The looped-model community is small enough that a careful theory paper could be useful, especially if it contributes testable predictions rather than branding-level overlap.

Potential paths:

- public discussion around the relevant papers / repos
- public preprint as a conversation starter
- direct outreach after Paper 1 or a framework note is public

This is a consideration, not an immediate action.

---

## 6. Immediate Actions

**Within the next 2 weeks (Phase 0):**

1. Update `docs/evidence-mapping.md` to add Parcae and OpenMythos entries, while explicitly distinguishing empirical evidence from speculative reconstruction.
2. Add a brief note to `docs/paper1/outline.md` §7 Discussion mentioning the looped-transformer connection with Parcae as the empirical anchor.
3. Keep `docs/preregistration/v1.2.md` unchanged. The current pre-registration should remain narrow.
4. Monitor:
   - arxiv.org for new looped-transformer papers
   - the Parcae GitHub / project page for released checkpoints and code details
   - the OpenMythos repository for architecture changes that affect its usefulness as an analysis sandbox

**Within Phase 1 (M3-M5):**

5. When testing H1a on TinyLoRA, log which task directions are found. If time permits, compare them to publicly available SAE feature lists. This helps prepare H1c-style analysis and later Paper 2 framing.
6. Read the Parcae codebase or released artifacts closely enough to understand the stability mechanisms. No experiment is required yet.

**Within Paper 1 writing (M11-M14):**

7. In §2 Related Work, add a paragraph on looped/shared-parameter models (Parcae, Universal Transformer, ALBERT, and, if useful, OpenMythos with explicit caveat).
8. In §7 Discussion, connect TinyLoRA update geometry to a broader substrate hypothesis in a deliberately cautious way.
9. Do not add H4 to Paper 1 unless Phase 1 finishes early and the controls are good enough to support the extra claim.

---

## 7. Summary

**Paragraph for Paper 1 §7 Discussion (draft):**

> Our finding of a shared adaptive subspace in parameter updates has possible parallels to recent work on looped and recurrent-depth transformers. Parcae (Prairie et al., 2026) provides empirical evidence that shared-parameter looped architectures can be trained stably and can achieve strong parameter efficiency, while recent reconstruction efforts such as OpenMythos illustrate how looping, routing, and adaptive halting might be combined in a broader design family. These works do not, by themselves, explain why a shared parameter block can support rich task-specific behavior. One candidate interpretation, consistent with our results, is that task-specific adaptation and looped computation both rely on steering a shared internal substrate rather than injecting large amounts of new task-specific machinery. Our experiments do not directly test recurrent hidden-state dynamics, so this connection should be treated as a hypothesis rather than a conclusion. But if future work finds analogous low-dimensional structure in both parameter updates and looped hidden-state trajectories, that would support a more general view of efficient adaptation as control over structured substrate.

**One-line summary:**

> ASCENT and recent looped transformers may be probing a related structural principle from different angles: Parcae provides empirical evidence that shared-parameter looping can work, while ASCENT offers testable hypotheses about what internal geometry might make that reuse effective; OpenMythos is useful as a reconstruction artifact, not as ground truth about Anthropic.

---

## Appendix A: Key References

- **Parcae:** Prairie, H., Novack, Z., Berg-Kirkpatrick, T., & Fu, D. Y. (2026). *Parcae: Scaling Laws For Stable Looped Language Models.* arXiv:2604.12946.
- **OpenMythos:** Gomez, K. (2026). *OpenMythos: A Theoretical Reconstruction of the Claude Mythos Architecture.* GitHub repository: github.com/kyegomez/OpenMythos.
- **Universal Transformer:** Dehghani, M. et al. (2018). arXiv:1807.03819.
- **ALBERT:** Lan, Z. et al. (2019). arXiv:1909.11942.
- **Reasoning with Latent Thoughts:** Saunshi, N. et al. (2025). arXiv:2502.17416.
- **Relaxed Recursive Transformers:** Bae, S. et al. (2024). arXiv:2410.20672.
- **Coconut (Continuous Latent Reasoning):** Hao et al. (2024). arXiv:2412.06769.

---

## Appendix B: Notes on Kye Gomez / OpenMythos

kyegomez is known for rapid open-source reconstructions of proprietary or rumored systems. The OpenMythos repository's "theoretical reconstruction" disclaimer is therefore central, not incidental: it is a hypothesis about a possible Mythos-like structure, not a leak and not an authoritative description of Anthropic's system.

This means:

1. OpenMythos is valuable as a testable hypothesis and software artifact, not as authoritative truth.
2. ASCENT's framework can still be applied to the architecture ideas it packages.
3. Academic writing should cite OpenMythos as a reconstruction / repository with explicit caveat.
4. Claims about Claude Mythos itself should not be built on OpenMythos alone.

---

*This document is a working draft. It may later be split into smaller artifacts: a Related Work paragraph for Paper 1, a Section in Paper 2, or a standalone post summarizing the connection. For now, it serves as the canonical internal analysis.*
