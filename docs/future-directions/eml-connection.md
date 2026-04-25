# EML Operator and ASCENT

**Document Type:** Future Research Direction / External Connection  
**Status:** Non-committed (Not part of pre-registration)  
**Related Paper:** Odrzywolek (2026), *All elementary functions from a single operator*, arXiv:2603.21852  
**Date:** 2026-04-22

---

## 1. Purpose

This document captures the structural parallels between the recently discovered **EML operator** and ASCENT's central claim about task adaptation. It identifies three concrete uses of the connection and explicitly marks what must **not** be claimed.

Clarification:

> EML is **not a mechanism** in ASCENT.  
> EML is a **structural analogy** that informs ASCENT's framing and motivates one exploratory extension.

---

## 2. Summary of EML (Odrzywolek, 2026)

### Central finding

A single binary operator

```text
eml(x, y) = exp(x) - ln(y)
```

together with the constant `1`, suffices to express all standard elementary functions (`+`, `-`, `*`, `/`, `sin`, `cos`, `log`, `sqrt`, `pi`, `e`, `i`, etc.) as binary trees of EML nodes.

```text
e^x      = eml(x, 1)
ln(x)    = eml(1, eml(eml(1, x), 1))
e        = eml(1, 1)
-1, pi, i = larger EML trees
```

### Three contributions

1. **Theoretical:** Discovery of a continuous Sheffer operator, the analog of NAND for continuous mathematics.
2. **Computational:** An EML compiler (Python -> Rust, about 1000x speedup) converts any elementary formula to pure-EML form.
3. **Empirical (symbolic regression):** Gradient-based optimization of EML tree weights recovers exact closed-form expressions when the generating law is elementary. Depth 2 gives 100% recovery from random initialization; depth 5 drops below 1%; depth 6+ becomes unreliable. But when initialized near a correct solution with Gaussian perturbation, recovery returns to 100% even at depth 6.

### Why it matters beyond symbolic regression

The core discovery is **reducibility**: the apparent diversity of elementary functions masks a single underlying generative primitive. This pattern has historical precedent:

- NAND for Boolean logic (Sheffer, 1913)
- Euler's formula for trigonometric functions
- DNA as a near-universal information carrier
- S and K combinators in combinatory logic

EML adds continuous mathematics to this lineage.

---

## 3. Three Structural Parallels with ASCENT

### 3.1 Parallel 1 - Reducibility to a Primitive

**EML:**  
Apparent diversity of elementary functions reduces to one operator (`eml`) plus one constant (`1`).

**ASCENT:**  
Apparent diversity of task-specific adaptations is hypothesized to reduce to one substrate (pretrained weights) plus small task-specific control signals.

**Shared pattern:**

> In both cases, surface-level complexity masks a simpler underlying generative structure.

**How to use this in ASCENT:**  
As a framing device for the Paper 1 introduction, not as a technical claim.

### 3.2 Parallel 2 - Basin of Attraction

**EML finding:**

- Random initialization makes correct formula recovery rare at high depth
- Perturbed-correct initialization gives near-100% recovery even at depth 6
- Correct solutions therefore appear to sit in stable basins: hard to find from random init, easy to stay in once found

**ASCENT analog (proposed):**  
If task-specific TinyLoRA updates `u_i` form a shared adaptive subspace (H1a), then:

- the subspace may have basin-like geometry,
- perturbed correct solutions should recover,
- random directions should not.

This would be a **dynamical check** on the geometric H1a hypothesis.

**Proposed exploratory experiment (post-Phase 2):**

```text
Input: task t, trained TinyLoRA update u*_t (from Phase 1)

Protocol:
  1. Create perturbed version: u_pert = u*_t + e, where e ~ N(0, sigma^2)
  2. Re-train from u_pert for K additional steps -> u_recovered
  3. Measure distance: d_recover = ||u_recovered - u*_t||

  4. Control: random direction u_random (same norm as u*_t)
  5. Re-train from u_random -> u_random_final
  6. Measure: d_random = ||u_random_final - u*_t||

Hypothesis:
  d_recover << d_random
  (indicating u*_t sits in a stable basin)

Alternative:
  d_recover ~= d_random
  (indicating no basin structure; undermines H1a's geometric claim)
```

**Status:** Exploratory. Not part of Paper 1 pre-registration. Default planning assumption: do not add this to the current Paper 1 roadmap or registered analysis set. Consider it only if the main paper draft is already stable and surplus time remains.

### 3.3 Parallel 3 - Operator-Algebraic Interpretation

**EML pattern:**  
All functions are compositions of one operator. Grammar:

```text
S -> 1 | eml(S, S)
```

**ASCENT operator view** (formalizing the SAGE future-direction note):

Define the per-task implicit operator:

```text
O_i(z) := z + u_i
```

where `u_i` is the task-specific update vector.

Under this view:

- **Task** = operator `O_i`
- **Adaptation** = selecting the right operator
- **H1a (shared subspace)** = operators `{O_i}` live in a low-dimensional algebra

**Connection to continuous architectures:**

| Mechanism | Interpreted As |
|-----------|----------------|
| LoRA update | Task operator `O_i` |
| Attention | Binding operator |
| MoE routing | Expert operator selection |
| Loop iteration (Parcae/Mythos) | Repeated operator application |

**Important caveat:**  
This operator view is a reinterpretation, not a new mathematical structure. It helps unify ASCENT's substrate-control claim with architectural variants (looped, MoE, LoRA), but it does not introduce new computation.

**Where to use:**

- Paper 2 Section 4 (unified framework for looped/shared/modular architectures)
- Paper 2 Discussion (connection to combinatory logic tradition)
- Do **not** use in Paper 1, which should stay scoped to geometric evidence

---

## 4. Rhetorical Use in Paper 1 Introduction

### Draft paragraph candidate

> "The history of mathematics contains striking examples of apparent diversity reducing to a single underlying primitive. Boolean logic reduces to NAND (Sheffer, 1913). Elementary functions - trigonometric, logarithmic, algebraic - recently were shown to reduce to a single binary operator, EML (Odrzywolek, 2026). In this paper, we ask whether a similar reduction may hold for task-specific adaptations of pretrained language models: does the apparent diversity of task-specific updates conceal a simpler underlying structure? Recent evidence from TinyLoRA (Morris et al., 2026) - where 13 trained parameters recover most of the performance of full fine-tuning on reasoning benchmarks - suggests this may be the case, but only when using reinforcement learning. We investigate this possibility geometrically, asking whether task-specific updates share a low-dimensional subspace."

### Usage constraints

- **Do:** Use as one opening analogy among others; the TinyLoRA paradox remains primary.
- **Do:** Cite EML briefly when discussing structural reducibility.
- **Do not:** Claim that ASCENT reduces task adaptation to a single operator. ASCENT claims a substrate, not a single primitive.
- **Do not:** Overstate the parallel. EML is a proven mathematical reduction; ASCENT is a hypothesis about statistical learning.

---

## 5. Hardware Implications (Custom SoC Angle)

This section is speculative but potentially patent-relevant.

### 5.1 EML as analog or mixed-signal primitive

The paper notes that EML can be implemented as:

- an analog circuit,
- an FPGA-friendly uniform binary tree,
- a single-instruction stack machine (RPN-style).

Current neural accelerators (NPUs, TPUs) are optimized for matrix multiplication (GEMM). They are weak at:

- sparse recursive scalar computation,
- interleaved symbolic and numeric work,
- tree-structured graph operations.

EML suggests a complementary computing unit for scalar-recursive or event-based tasks.

### 5.2 Relevance to ASCENT-derived hardware direction

From the SAGE future-direction document, ASCENT suggests:

- `B_adaptive` is small, so execution may be sparse,
- only a few task-relevant subspaces activate at a time,
- dense all-token computation may be wasteful.

EML's tree-based recursion is a concrete example of **non-GEMM compute**. It does not directly apply to transformer inference, but it points toward architectures like:

```text
Current NPU:         GEMM array + ReLU/GELU postprocessor
Hybrid direction:    GEMM array + recursive scalar primitive unit
                     (tree execution engine)
```

### 5.3 Patent direction (candidate themes)

These are candidate themes, not drafted claims, and require legal review.

- **Theme A:** Hybrid neural accelerator combining a tensor matrix engine with a recursive scalar operator engine for symbolic or structured computation.
- **Theme B:** Runtime scheduling between matrix execution for dense layers and recursive scalar execution for compact expression-tree operations such as custom activations, elementary-function approximation, or postprocessing.
- **Theme C:** Event-driven execution of neural sub-components based on substrate activation patterns, connected to ASCENT's sparse subspace activation thesis.

**Strategy note:**  
These claims are independent of whether ASCENT-G succeeds. They respond to a broader need for non-GEMM acceleration in scientific computing and symbolic AI, so they should be tracked separately from core ASCENT claims.

### 5.4 What not to claim

- Do not claim a production NPU should become an EML machine.
- Do not claim EML solves LLM inference efficiency.
- Do not claim ASCENT's substrate is an EML-like structure.

---

## 6. What EML Does Not Contribute

To prevent overreach:

1. **EML does not describe LLM internal computation.** LLMs are statistical; EML is symbolic.
2. **EML's depth-4 to depth-6 regime is tiny.** LLM equivalents would be far deeper.
3. **EML requires complex arithmetic internally.** That overhead does not map cleanly onto standard NPU design.
4. **The reducibility parallel is philosophical, not formal.** ASCENT is falsifiable empirically; EML's completeness is mathematically proven.
5. **No ASCENT result depends on EML.** If EML were withdrawn tomorrow, ASCENT's experiments and conclusions would be unchanged.

---

## 7. Recommended Usage in ASCENT Documents

| Location | Usage | Level |
|----------|-------|-------|
| Paper 1 Section 1 (Intro) | One-paragraph rhetorical parallel | Light |
| Paper 1 Section 2 (Related Work) | Brief citation in structural reducibility context | Minimal |
| Paper 1 Section 6 (Exploratory) | Basin-of-attraction experiment only after main writing goals are met | Deferred / conditional |
| Paper 2 Section 4 (Unified framework) | Operator-view formalization | Medium |
| Paper 2 Section 7 (Discussion) | Connection to combinatory logic / universal primitives | Light |
| Patent filings | Independent hardware claim themes | Separate track |
| OSF pre-registration | Not used | None |

Current planning note:

- This document does not change the Paper 1 preregistration, hypothesis set, methodology, `ascent-geometry` code plan, or roadmap milestones.

---

## 8. Integration with Other Future Directions

This document sits alongside:

- `sage.md` (SAGE-LM reframed as interpretation lens)
- future `mythos-connection.md` (looped transformers analysis)

Relationship map:

```text
                 ASCENT (core)
                      |
        +-------------+-------------+
        |             |             |
      sage.md       eml.md    mythos-connection.md
     (operator    (reducibility    (architecture
      view)       + HW angle)      evidence)
```

Each document is a lens on ASCENT's central claim from a different direction. None modifies the core framework.

---

## 9. Decision Log

| Date | Decision | Rationale |
|------|----------|-----------|
| 2026-04-22 | Create `eml-connection.md` as separate file | Enough content to merit clean separation |
| 2026-04-22 | Do not add EML experiment to pre-registration | Protects Paper 1 scope; remains exploratory |
| 2026-04-22 | Reserve hardware/patent themes as an independent track | Reduces coupling risk between framework and IP |
| 2026-04-22 | Reject operator algebra as a Paper 2 central claim | Keep it as an interpretive device |

---

## 10. One-line Takeaway

> EML is evidence that apparent diversity can collapse to a single primitive.  
> ASCENT asks whether task adaptation may exhibit an analogous collapse.  
> Evidence must come from experiments, not from the parallel itself.

---

*This document is informational. It does not commit ASCENT to run any experiment described here or to file any patent claim referenced here. Before acting on hardware or patent content, consult internal IP counsel or the relevant legal/IP team.*
