# ASCENT Framework: Future Directions

This document consolidates architecture-oriented material that previously lived in the draft workspace before promotion into the canonical `docs/` tree.

It is part of the canonical `docs/` tree, but it is **not** part of the registered claim set for Paper 1. Its role is to preserve the medium-term design direction without mixing it into the empirical core.

---

## 1. Role of This Document

Paper 1 asks a narrow question:

> Do tiny task updates reveal a shared, structured adaptation geometry in pretrained models?

This document addresses the follow-on question:

> If that geometry is real, what kind of architecture or decomposition should it motivate?

Nothing here should be cited as already validated by the current experiments.

---

## 2. Long-Term Direction

The longer-term design space centers on five connected ideas:

- compact reasoning or dynamics,
- separated or structured memory,
- reusable capability substrate,
- self-similar operations across time or scale,
- component-aware structural priors.

The underlying thesis is that a future ASCENT-style model should be described less as one monolithic dense parameter block and more as a control system operating over reusable internal structure.

---

## 3. Geometry First, Architecture Second

The strongest design rule carried over from the draft workspace is:

> Measure adaptation geometry first, then let architecture follow the measurements.

This rule exists to prevent three common failures:

- proposing a new architecture before the substrate claim is empirically grounded,
- using speculative memory or controller language as evidence,
- letting long-term ideas blur the scope of Paper 1.

In practical terms:

- `ASCENT-G` tests whether adaptation has shared low-dimensional structure,
- later work can ask how that structure should be implemented architecturally.

---

## 4. Candidate Architecture Thesis

If the adaptation-geometry results are positive, the natural architectural direction is:

- a small control or dynamics core,
- operating over a structured capability substrate,
- with knowledge not forced to live only in dense weights,
- and with memory access separated from control where useful.

This is the draft-space version of the earlier "small controller over structured capabilities" idea. It remains a future design target, not an active repository commitment.

---

## 5. Component-Aware Priors

One of the most useful ideas from the draft workspace is that different model components may admit different structural priors.

### 5.1 Positional Encoding

Candidate prior:

- already close to closed-form structure.

This is the cleanest example of structure that does not need to be rediscovered by optimization.

### 5.2 Attention Projections

Candidate prior:

- low-rank structure,
- task-specific subspace movement,
- reusable linear motifs.

This is the most direct bridge between update-geometry experiments and later architectural compression or routing ideas.

### 5.3 Attention Heads

Candidate prior:

- circuit-level decomposition,
- motif or algorithm-level specialization,
- functional rather than purely matrix-level structure.

### 5.4 FFN Blocks

Candidate prior:

- sparse dictionary view,
- overcomplete feature basis,
- interpretable feature superposition.

This is the component family most naturally connected to SAE-style evidence.

### 5.5 Residual Stream

Candidate prior:

- shared feature space,
- alignment layer across components,
- candidate carrier of cross-layer structure.

The residual stream is where component coupling becomes unavoidable, which is why it matters for any later architecture proposal.

---

## 6. Dynamics and Memory Separation

Another recurring draft-space proposal is a system built around:

- a compact latent state,
- a recurrent update rule,
- a memory interface,
- and a decoder from latent state to output.

In that framing:

- intelligence is concentrated in dynamics,
- knowledge is externalized or structurally separated,
- repeated update in time replaces part of the need for a large dense stack.

This idea is intentionally kept here rather than in `framework/v6.1.md`, because Paper 1 does not test this architecture directly.

---

## 7. Self-Similarity, Fractals, and Hierarchy

The draft workspace used "fractal" language heavily. The canonical interpretation should remain conservative.

Useful interpretation:

- self-similarity is a measurable property,
- hierarchy is a design and representation property,
- repeated operations may recur across layers or timescales.

Rejected interpretation:

- "the LLM is a fractal" as an ontological claim.

Canonical rule:

- treat self-similarity as measurement language or future architecture intuition,
- not as evidence by itself.

---

## 8. Working Roadmap From Here

If Paper 1 succeeds, the architecture program should proceed roughly in this order:

1. Confirm which update objects reveal the cleanest shared structure.
2. Determine whether reusable capability is better described as features, subspaces, motifs, or modules.
3. Test whether component-specific priors help explain the measured geometry.
4. Only then prototype a model with compact dynamics and structured capability access.

If Paper 1 fails, this entire document remains speculative design space and should not be promoted into stronger claims.

---

## 9. Source Consolidation Note

This file consolidates the earlier draft-space materials that treated long-term architecture, component priors, and geometry-first design as separate notes.

Those earlier draft files are now superseded by the canonical `docs/` structure.
