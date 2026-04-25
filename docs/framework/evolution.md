# ASCENT Framework: Evolution History

This document traces how the framework evolved from the initial idea to v6.1.  
It serves as intellectual history and is preserved for transparency.

For the current canonical framework statement, see `v6.1.md`.
For architecture-facing synthesis that was previously scattered through the draft
workspace, see `../future-directions/index.md`.

---

## Summary Table

| Version | Period | Central Idea | Key Shift |
|---------|--------|--------------|-----------|
| v1 | Feb 2026 | "LLM has hidden math structure" | — |
| v2 | Mar 2026 | Component decomposition + fractal intuition | Structure → Components |
| v3 | Mar 2026 | Fractal capability library | Compression → Library |
| v4 | Mar 2026 | Two-level controller + substrate | Library → Dynamic substrate |
| v5 | Apr 2026 | Timescale hierarchy + bit decomposition | Richer structure |
| v6 | Apr 2026 | Single central claim + adaptation geometry first | Expansion → Compression |
| v6.1 | Apr 18, 2026 | Task-relativity explicit + recovery path | Hardening |

---

## v1: The Origin (February 2026)

**Starting point:** `operator-coordinate-compression` repository

**Core question:**
> If a physical phenomenon is well-described by a simple sine-wave sum, and we train an MLP to reproduce it, what is the relationship between the MLP weights and the original mathematical model?

**Original claim:**
> LLM compression is not about removing information, but about choosing the right coordinate system for operator parameters.

**Key insight preserved:**
- Outliers are coordinate artifacts, not semantic content
- Rotation ≠ frequency decomposition (it's axis transformation)
- Flattening (what rotation achieves) ≠ concentration (what compression needs)

**Limitation of v1:**
- Focused on compression of existing weights
- No clear path to "what should replace dense LLM"
- "Hidden math structure" claim too strong

---

## v2: Component Decomposition (March 2026)

**New elements:**
- Different compression priors for different components (attention Q/K/V vs FFN)
- Analogy to image compression (DCT for smooth, wavelet for edges)
- First mention of LLM as compositional structure

**Key analogies introduced:**
- JPEG/DCT → structural prior for natural images
- LPC (speech coding) → model + residual decomposition
- MPEG motion compensation → different models for different components

**Limitation of v2:**
- Still compression-focused
- "Each component needs a different prior" = true but didn't know what priors

---

## v3: Fractal Capability Library (March 2026)

**Trigger:** TinyLoRA paper (Morris et al., 2026) — 13 parameters → 91% GSM8K

**New elements:**
- LLM as "capability library"
- Task adaptation = library selection, not knowledge injection
- Fractal/self-similar structure hypothesis
- First mention of controller

**Key realization:**
> TinyLoRA's RL success (vs SFT failure) suggests RL activates existing capabilities rather than injecting new ones.

**Limitation of v3:**
- "Library" framing too static (discrete units)
- Fractal claim too strong as central metaphysics
- Framework too large for a single paper

---

## v4: Controlled Capability Substrate (March 2026)

**Key shifts:**
- "Library" → "Substrate" (more dynamic, allows superposition)
- Two-level controller: c^slow (task) + c^fast (input)
- Space-time dynamics formalized
- Substrate hierarchy introduced (timescales)

**New equations:**
```
z^(ℓ+1,t) = F_θ(z^(ℓ,t); γ_ℓ, c^slow)
z^(ℓ,t+1) = G_φ(z^(ℓ,t), x_t, m^(ℓ,t), c^fast_t)
```

**Architecture proposal emerged:**
- Small dynamics core G
- Structured capability memory L
- Hierarchical modulation γ_ℓ

**Limitation of v4:**
- Architecture paper was being proposed before geometry was verified
- Training dynamics unresolved (gradient bottleneck)
- Three gaps unaddressed: library unit, controller source, self-similarity vs hierarchy

---

## v5: Timescale Hierarchy + Bit Decomposition (April 2026)

**New elements:**
- Four-layer substrate: {S^fast, S^slow, S^base, S^meta}
- B_model = B_shared + B_adaptive decomposition
- Controller capacity bound (design principle, not theorem)
- H1a/H1b/H1c ontology ladder (hierarchical claim strength)
- Space-time integration (depth × time axes)
- Mamba/SSM connection explicit

**Key insight:**
> B_adaptive[T] ≈ TinyLoRA update size. This is measurable. Task complexity should predict B_adaptive minimum.

**Limitation of v5:**
- Framework too large for one paper (7 major elements)
- Architecture-first before geometry verified
- Task-distribution relativity implicit, not explicit

---

## v6: The Compression Turn (April 2026)

**Decision:** Stop expanding. Start narrowing.

**Codex judgment:**
> "The first paper's battle is not to make people believe a new architecture, but to show that small adaptation is actually a structured control problem."

**Single central claim:**
> Task adaptation in pretrained LLMs is primarily a control problem over a pretrained capability substrate, not a knowledge injection problem.

**Deferred to later papers:**
- Architecture proposal → ASCENT-A
- World-model curriculum → ASCENT-D
- Fractal dimension as central claim → measurement tool only
- Controller capacity theorem → design principle only

**Retained as supporting structures:**
- B_shared/B_adaptive decomposition
- Substrate hierarchy
- Space-time dynamics
- H1a/H1b/H1c ontology ladder

**First paper reframed:**
- Not: "Here's a new architecture"
- Yes: "Do tiny updates reveal the adaptation geometry of pretrained models?"

---

## v6.1: Hardening (April 18, 2026)

**Final adjustments before OSF registration:**

1. **Task-relativity explicit:** B_shared, B_adaptive are always relative to T. "Universal" claims removed.
2. **Recovery path added:** If H1a fails, paper becomes "Understanding TinyLoRA without structured subspaces."
3. **H1b circularity fixed:** Separability measured after projection, not in ambient space.
4. **Gray zone defined:** H1a has 4 tiers (strong pass / marginal / inconclusive / fail).
5. **Compute budget:** N ≥ 10 minimum, bootstrap CI stopping criterion.
6. **Model names:** Concrete model identities specified pre-registration.

**Status: STABLE**  
v6.1 is the registered framework. No further changes without amendment logging.

---

## What Was Tried and Discarded

### "Hidden mathematical formula" framing (v1)

*Why discarded:* Too strong. Implies closed-form structure that may not exist. LLM complexity may genuinely require neural representation. Better: "redundancy can be removed" (weak, empirical) rather than "formula exists" (strong, unverifiable).

### "LLM is a fractal" as ontological claim (v3)

*Why discarded:* Fractal is a metaphor, not a falsifiable property of LLMs. Converted to measurement tool: "does self-similar structure appear in layer weights?" (H3, exploratory).

### "Architecture-first" positioning (v3-v5)

*Why discarded:* Architecture proposal without geometric evidence is speculation. Geometry paper first, architecture second. This is both scientifically stronger and more practical (smaller scope for Paper 1).

### H3 in hard falsification (v5 attempt)

*Why discarded:* H3 (self-similarity) is one step removed from central claim. Including it in hard falsification would mean the paper's core finding depends on an exploratory result. Cleaner: H3 is exploratory, doesn't affect central claim determination.

---

## Lessons for Future Iterations

1. **Expand first, then compress.** Versions 1-5 needed to explore the full space. Version 6 needed to cut. Both phases were necessary.

2. **The central claim should be falsifiable in one experiment.** "Adaptation is control over substrate" → H1a test. If H1a doesn't test the central claim, the claim needs revision.

3. **Evidence drives claim strength.** The ontology ladder (H1a/b/c) is not just methodology — it's intellectual honesty. Don't claim "interpretable substrate" based on H1a alone.

4. **Negative results need a plan.** Recovery path ensures the project has value regardless of H1a outcome. Designing this before experiments removes confirmation bias pressure.

5. **Don't conflate "interesting idea" with "paper-ready claim."** Many ideas in v3-v5 are interesting but not yet evidence-supported. Paper 1 tests one thing well. Later papers test the rest.
