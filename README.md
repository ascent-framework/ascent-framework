# ASCENT Framework

**Adaptation as Selection on Capability Networks and Topology**

> *"Tiny updates, large selections."*

---

## What is ASCENT?

ASCENT is a research framework proposing that **task adaptation in large language models is primarily a control problem over a pretrained capability substrate**, not a knowledge injection problem.

The key observation: [TinyLoRA (Morris et al., 2026)](https://arxiv.org/abs/2602.04118) shows that training only **13 parameters** can recover 90%+ of full fine-tuning performance on hard reasoning benchmarks, but only with reinforcement learning, not supervised fine-tuning. This asymmetry suggests that RL is not injecting new knowledge, but rather **activating existing capabilities** already latent in the pretrained model.

This framework formalizes that intuition, provides a measurement methodology, and develops its architectural implications.

---

## Central Claim

> **Task adaptation in pretrained LLMs is primarily a control problem over a pretrained capability substrate, not a knowledge injection problem.**

Formally:

```
B_model[T] = B_shared[T] + B_adaptive[T]
```

Where:

- `T` = fixed task distribution (e.g., reasoning benchmarks)
- `B_shared[T]` = information shared across tasks in `T` (resides in substrate)
- `B_adaptive[T]` = task-specific control cost (what TinyLoRA measures)

The hypothesis: `B_adaptive[T]` is surprisingly small for many task families, because the pretrained substrate already contains the necessary capability structure.

---

## Repository Organization

This GitHub organization contains:

```
ascent-framework/  You are here: docs, theory, pre-registration
ascent-geometry/   Paper 1 experiments (H1a/b/c + H2) [planned]
ascent-arch/       Paper 2 architecture prototype [planned]
```

### This Repository Structure

```
ascent-framework/
|- README.md
|- ROADMAP.md
|- .github/
|  `- SETUP.md
|- docs/
|  |- index.md
|  |- framework/
|  |  |- v6.1.md
|  |  `- evolution.md
|  |- preregistration/
|  |  |- v1.2.md
|  |  `- v1.3.md
|  |- paper1/
|  |  |- outline.md
|  |  `- evidence-mapping.md
|  |- exploration/
|  |  `- compute-options.md
|  |- future-directions/
|  |  |- index.md
|  |  |- eml-connection.md
|  |  |- paper1-vs-paper2.md
|  |  `- stylespace-styleclip-connection.md
|  `- archive/
`- .gitignore
```

Current repo note:

- the current preregistration reference is `docs/preregistration/v1.3.md`,
- future-direction notes live under `docs/future-directions/`,
- archived predecessor documents are stored under `docs/archive/operator-coordinate-compression/`,
- earlier ASCENT note archives are preserved under `docs/archive/ascent-framework-notes/`.

---

## The Three Papers

| Paper | Title | Status |
|-------|-------|--------|
| **ASCENT-G** | *Adaptation Geometry in Pretrained Language Models* | Active |
| **ASCENT-A** | *Small Controllers over Structured Capabilities* | Planned (M13+) |
| **ASCENT-D** | *Developmental Substrate Composition* | Future (M25+) |

---

## Hypotheses Under Investigation (Pre-registered)

| Hypothesis | Description | Type |
|-----------|-------------|------|
| **H1a** | Task update vectors share a low-dimensional subspace | Primary |
| **H1b** | Task directions are separable within that subspace | Primary |
| **H2** | Updates transfer across similar base models | Primary |
| **H1c** | Directions align with interpretable features | Exploratory |
| **H3** | Layer weights show statistical self-similarity | Exploratory |

Full methodology with falsification criteria: [`docs/preregistration/v1.3.md`](docs/preregistration/v1.3.md)

---

## Substrate Hierarchy

The pretrained capability substrate operates at multiple timescales:

```
S = { S^fast, S^slow, S^base, S^meta }
```

| Layer | Updated By | Example |
|-------|------------|---------|
| `S^fast` | In-context dynamics | KV cache evolution |
| `S^slow` | Fine-tuning / TinyLoRA | Task-specific adapters |
| `S^base` | Pre-training | Base LLM weights |
| `S^meta` | Architecture design | Attention structure |

---

## Key Equations

**Space-time dynamics:**

```
z^(l+1, t) = F_theta(z^(l, t); theta_shared, c^slow)   [depth axis]
z^(l, t+1) = G_phi(z^(l, t), x_t, m^(l, t), c^fast_t)  [time axis]
m^(l, t)   = Read(q^(l, t), L^(l))                     [memory access]
```

**Information decomposition:**

```
B_model[T] = B_shared[T] + B_adaptive[T]
```

---

## Evidence Base

| Prior Work | ASCENT Connection |
|-----------|------------------|
| TinyLoRA (Morris et al., 2026) | Small `B_adaptive` empirical evidence |
| Universal Transformer (2018) | Shared `F_theta` across depth |
| ALBERT (2019) | Cross-layer parameter sharing |
| Mamba (2023) | Selective time dynamics `G_phi` |
| Anthropic SAE (2024+) | Interpretable substrate structure |
| Ansuini et al. (2019) | Low intrinsic dimension |

Full mapping: [`docs/paper1/evidence-mapping.md`](docs/paper1/evidence-mapping.md)

---

## Project Timeline Summary

| Month | Milestone |
|-------|-----------|
| M2 | OSF pre-registration public + code skeleton |
| M5 | H1a decision (pass / marginal / inconclusive / fail) |
| M10 | H1-H2 complete, central claim verdict |
| M14 | Paper 1 full draft |
| M18 | Workshop submission + arXiv preprint |
| M22 | Patent filed |
| M26 | Conference paper |
| M30 | Paper 2 prototype |

Full plan: [`ROADMAP.md`](ROADMAP.md)

---

## Status

```
Framework version:    v6.1 (stable)
Pre-registration:     v1.3 (OSF public)
Experiments:          Phase 0 / Foundation
Current phase:        Setup + pilot preparation
```

---

## Background

ASCENT evolved from [`operator-coordinate-compression`](https://github.com/che-yjwj/operator-coordinate-compression), which investigated LLM weight compression through coordinate system selection. Core insight preserved: **the geometry of parameter space determines compression efficiency**. Key evolution: from "find the right coordinates" to "understand the control structure of the substrate."

The previous project's documents are preserved in `docs/archive/operator-coordinate-compression/`.

---

## Disclaimer

*ASCENT is an independent research project conducted outside of working hours.  
Views and research are personal and do not represent any employer.*

---

## License

- Code: [MIT License](LICENSE)
- Documents: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)
