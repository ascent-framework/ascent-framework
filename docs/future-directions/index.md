# ASCENT Framework: Future Directions

This directory is the canonical home for ASCENT future-direction notes.

These documents are part of the repository's long-range design record, but they are **not** part of the registered claim set for Paper 1. Their role is to preserve architectural and conceptual directions without mixing them into the empirical core.

## Quick Guide

- Read `scope/paper1-vs-paper2.md` first if you need the scope boundary between evidence and architecture.
- Read `connections/eml-connection.md` for an English external-analogy note tied to framing and hardware direction.
- Read `connections/stylespace-styleclip-connection.md` for a Korean note on editing-style analogies and why they matter more for Paper 2 than Paper 1.
- Treat this directory as design-space documentation, not as validated empirical evidence.

---

## 1. Role of This Directory

Paper 1 asks a narrow question:

> Do tiny task updates reveal a shared, structured adaptation geometry in pretrained models?

The documents here address the follow-on question:

> If that geometry is real, what architecture, decomposition, or hardware direction should it motivate?

Nothing in this directory should be cited as already validated by the current experiments.

---

## 2. Core Design Rule

The strongest rule carried forward from the draft workspace is:

> Measure adaptation geometry first, then let architecture follow the measurements.

This rule exists to prevent three failures:

- proposing a new architecture before the substrate claim is empirically grounded,
- using speculative controller or memory language as evidence,
- letting long-term ideas blur the scope of Paper 1.

In practical terms:

- `ASCENT-G` tests whether adaptation has shared low-dimensional structure,
- later work can ask how that structure should be implemented architecturally.

---

## 3. Long-Term Direction

The longer-term design space centers on five connected ideas:

- compact reasoning or dynamics,
- separated or structured memory,
- reusable capability substrate,
- self-similar operations across time or scale,
- component-aware structural priors.

The underlying thesis is that a future ASCENT-style model should be described less as one monolithic dense parameter block and more as a control system operating over reusable internal structure.

If the adaptation-geometry results are positive, the natural architectural direction is:

- a small control or dynamics core,
- operating over a structured capability substrate,
- with knowledge not forced to live only in dense weights,
- and with memory access separated from control where useful.

This remains a future design target, not an active repository commitment.

---

## 4. Component-Aware Priors

One recurring thesis is that different model components may admit different structural priors:

- positional encoding may already be close to closed-form structure,
- attention projections may favor low-rank or reusable linear motifs,
- attention heads may admit circuit-level or algorithmic decomposition,
- FFN blocks are the strongest candidates for sparse-dictionary and SAE-style views,
- the residual stream may act as the carrier of shared cross-component structure.

These are design hypotheses, not current empirical conclusions.

---

## 5. Dynamics, Memory, and Hierarchy

Another recurring direction is a system built around:

- a compact latent state,
- a recurrent update rule,
- a memory interface,
- and a decoder from latent state to output.

In that framing:

- intelligence is concentrated in dynamics,
- knowledge is externalized or structurally separated,
- repeated update in time replaces part of the need for a large dense stack.

Self-similarity language should remain conservative:

- use it as measurement language or architecture intuition,
- do not treat "the LLM is a fractal" as a claim by itself.

---

## 6. Current Notes

### Structure

- `scope/`: documents that define what belongs in Paper 1 versus later architecture work
- `connections/`: external analogy notes and design-space references
- `index.md` and `README.md`: entry points for browsing this directory

| Document | Role | Language | Relevance |
|----------|------|----------|-----------|
| [Paper 1 and Paper 2 Explained](./scope/paper1-vs-paper2.md) | Scope note separating measurement from architecture | EN | Read first; sets the rule for using the rest of this directory |
| [EML Operator and ASCENT](./connections/eml-connection.md) | External connection note on reducibility, operator framing, and hardware implications | EN | Secondary note; useful for framing and later architecture discussion |
| [StyleSpace / StyleCLIP and ASCENT](./connections/stylespace-styleclip-connection.md) | External connection note on controllable directions, editability, and analogy limits | KR | Secondary note; more relevant to Paper 2 than Paper 1 |

Planned future-direction notes should be added here only when they have an actual file and a defined role.

---

## 7. Working Roadmap

If Paper 1 succeeds, the architecture program should proceed roughly in this order:

1. Confirm which update objects reveal the cleanest shared structure.
2. Determine whether reusable capability is better described as features, subspaces, motifs, or modules.
3. Test whether component-specific priors help explain the measured geometry.
4. Only then prototype a model with compact dynamics and structured capability access.

If Paper 1 fails, the material in this directory remains speculative design space and should not be promoted into stronger claims.

---

## 8. Consolidation Note

This index replaces the previous split between exploratory future-direction material under `docs/exploration/` and note-specific files elsewhere.

Canonical rule:

- future-direction hub and note files live under `docs/future-directions/`,
- `docs/exploration/` should be used for operational exploration notes, not the canonical future-direction hierarchy.
