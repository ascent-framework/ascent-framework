# Paper 1 and Paper 2 Explained

**Document Type:** Clarification Note  
**Status:** Informational  
**Scope:** Explains the division of labor between ASCENT-G (Paper 1) and ASCENT-A (Paper 2)  
**Date:** 2026-04-25

---

## 1. Purpose

This note explains, in practical terms, why ASCENT is split into two papers:

- **Paper 1** asks whether the claimed structure is actually present.
- **Paper 2** asks what architecture should follow if that structure is real.

The distinction is important because many architecture ideas are interesting long
before they are justified. ASCENT's current rule is:

> Measure the geometry first. Design the architecture second.

---

## 2. One-Sentence Version

```text
Paper 1 = verify the existence of shared adaptation structure
Paper 2 = build a model that uses that structure
```

Or even shorter:

```text
Paper 1 = evidence
Paper 2 = design
```

---

## 3. Why Two Papers Instead of One?

Because the scientific burden is different.

### If everything is mixed together

```text
Claim:
  "LLMs adapt through a small controller over a structured substrate,
   therefore here is a new architecture."

Problem:
  The architecture is doing most of the rhetorical work before the substrate
  claim has been validated.
```

### If the work is split cleanly

```text
Paper 1:
  Does adaptation geometry actually look substrate-like?

Paper 2:
  If yes, what model family best exploits that fact?
```

This makes failure informative too:

```text
If Paper 1 fails:
  architecture claims should weaken or pause

If Paper 1 passes:
  architecture work becomes much more credible
```

---

## 4. Paper 1: ASCENT-G

**Working title:** *Adaptation Geometry in Pretrained Language Models*

### 4.1 Core question

Do task-specific parameter updates in pretrained LLMs lie in a shared,
low-dimensional structure?

### 4.2 Intuition

TinyLoRA suggests that a model may already contain much of the needed
capability, and that adaptation may only need a small task-specific steering
signal.

Paper 1 tries to test that idea directly in parameter space.

### 4.3 Main objects

For each task `t_i`, train a task-specific update vector:

```text
u_i in R^D
```

Stack them into a matrix:

```text
U = [u_1, u_2, ..., u_N] in R^(D x N)
```

Then analyze the geometry of `U`.

### 4.4 Main equations

**Singular value decomposition**

```text
U = V Sigma W^T
```

**90% energy rank**

```text
r_90 = min { k : sum_{i=1..k} sigma_i^2 / sum_{i=1..N} sigma_i^2 >= 0.9 }
```

**Shared-subspace ratio**

```text
rho = r_90 / min(N, sqrt(D_eff))
```

Interpretation:

- small `rho` means updates concentrate in a shared low-dimensional structure
- large `rho` means updates are spread out and do not compress well into a
  common subspace

### 4.5 What Paper 1 tests

```text
H1a: Do updates share a low-dimensional subspace?
H1b: Are task directions still separable inside that subspace?
H2:  Do those directions transfer across related models?
```

### 4.6 Paper 1 workflow

```text
pretrained model
      |
      v
task-specific GRPO / TinyLoRA training
      |
      v
update vectors u_1, u_2, ..., u_N
      |
      v
SVD / projection / transfer analysis
      |
      v
claim tier:
  fail / inconclusive / marginal / strong
```

### 4.7 What counts as success in Paper 1

Paper 1 is not trying to prove a full architecture. It is trying to establish
evidence like this:

```text
task diversity
   ->
shared update geometry
   +
directional separability
   +
transfer
   ->
"adaptation looks control-like"
```

### 4.8 What Paper 1 does not do

- It does **not** propose a production-ready architecture.
- It does **not** prove that a controller-substrate model is optimal.
- It does **not** make hardware claims.
- It does **not** depend on future-direction notes such as EML.

### 4.9 Mental model

Paper 1 is like surveying land before building a road.

```text
Question:
  "Is there really a stable path through this terrain?"
```

---

## 5. From Paper 1 to Paper 2

Paper 2 is justified only if Paper 1 produces enough evidence that adaptation is
not arbitrary.

The transition logic is:

```text
If updates are shared and structured,
then adaptation may be factored into:

  large reusable substrate
  + small task-sensitive control
```

In bit terms:

```text
B_model[T] = B_shared[T] + B_adaptive[T]
```

If Paper 1 suggests `B_adaptive[T] << B_shared[T]`, then Paper 2 asks:

```text
Why keep using a monolithic dense model
if the task-specific part is so small?
```

---

## 6. Paper 2: ASCENT-A

**Working theme:** small controllers over structured capabilities

### 6.1 Core question

If adaptation is mostly control over a reusable substrate, what kind of model
should implement that decomposition directly?

### 6.2 Intuition

Paper 2 takes the geometry result and turns it into an engineering question.

Instead of saying:

```text
"Fine-tune a giant dense block every time"
```

it asks whether the model should be organized more like:

```text
small dynamics / controller
          +
structured capability substrate
          +
selective memory access
```

### 6.3 Candidate decomposition

A simple way to express the idea is:

```text
state update:
  z_(t+1) = G_phi(z_t, x_t, m_t, c_t^fast)

shared operation across depth:
  z_(l+1,t) = F_theta(z_(l,t); gamma_l, c^slow)
```

Where:

- `F_theta` is a shared operation or reusable core
- `G_phi` is a compact dynamics update
- `c^slow` is task-level control
- `c^fast` is input-level or token-level control
- `m_t` is memory or structured substrate access

### 6.4 Paper 2 workflow

```text
Paper 1 evidence
      |
      v
derive architecture hypothesis
      |
      v
implement prototype
      |
      v
compare against baselines
  - standard Transformer
  - ALBERT / Universal Transformer style sharing
  - Mamba / SSM style dynamics
      |
      v
measure:
  performance
  efficiency
  controllability
  reuse
```

### 6.5 What Paper 2 tries to show

Paper 2 is not mainly asking whether shared geometry exists. Paper 1 already
asked that.

Paper 2 asks whether a model can be explicitly built around that structure:

```text
observed geometry
   ->
architectural principle
   ->
working prototype
   ->
empirical benefit
```

### 6.6 What Paper 2 depends on

Paper 2 depends conceptually on Paper 1 in the following sense:

```text
Paper 1 gives:
  "There appears to be a reusable substrate plus small control."

Paper 2 asks:
  "Can we make that decomposition native in the architecture?"
```

### 6.7 Mental model

Paper 2 is like designing the road and the vehicle after the land survey is
complete.

```text
Question:
  "Given the terrain, what is the right transport system to build?"
```

---

## 7. Side-by-Side Comparison

| Dimension | Paper 1 (ASCENT-G) | Paper 2 (ASCENT-A) |
|-----------|--------------------|--------------------|
| Main role | Measurement | Construction |
| Main question | Does shared adaptation geometry exist? | What architecture should exploit it? |
| Main object | Update vectors | Model design |
| Main tools | SVD, projections, transfer tests | Prototype implementation, baseline comparison |
| Main output | Evidence and claim tier | Working architecture and benchmark results |
| Failure mode | No shared structure detected | Prototype underperforms or decomposition is impractical |
| Status in roadmap | Active first | Planned after Paper 1 |

---

## 8. What Belongs in Each Paper

### Paper 1 belongs here

- TinyLoRA paradox
- LoRA / update-vector geometry
- H1a / H1b / H2
- SAE as interpretability support
- BitNet / Bonsai as **framing support only**
- EML as optional rhetorical analogy only

### Paper 2 belongs here

- controller-substrate architecture proposals
- shared-operation prototypes
- memory and routing design
- operator-view reinterpretations
- stronger use of EML-like or combinatory analogies
- hardware implications, if pursued

### Things that should stay out of Paper 1

- "Here is the new model architecture"
- "Here is the accelerator design"
- strong operator-algebra claims
- speculative hardware/software co-design arguments

---

## 9. Dependency Diagram

```text
                     ASCENT core claim
                           |
                           v
              +---------------------------+
              | Paper 1: ASCENT-G         |
              | Does adaptation geometry  |
              | reveal shared structure?  |
              +---------------------------+
                           |
                  if evidence is strong
                           |
                           v
              +---------------------------+
              | Paper 2: ASCENT-A         |
              | Can that structure be     |
              | built into the model?     |
              +---------------------------+
                           |
                           v
              prototype / systems / hardware
```

The key asymmetry:

```text
Paper 2 conceptually depends on Paper 1
Paper 1 does not depend on Paper 2
```

---

## 10. Relation to EML, Bonsai, and Other Side Notes

These supporting notes do not play the same role in the two papers.

### In Paper 1

- **EML:** at most a light rhetorical opening or a one-sentence citation
- **BitNet/Bonsai:** framing support for "structure matters more than precision"

### In Paper 2

- **EML:** more useful as an operator-view analogy
- **BitNet/Bonsai:** more useful for representation-agnostic substrate arguments
- **hardware notes:** more relevant because Paper 2 is closer to system design

So the same external idea can have very different weight depending on which
paper is being written.

---

## 11. Simplest Intuition

If you want the shortest possible explanation:

```text
Paper 1:
  "Is the hidden structure real?"

Paper 2:
  "If it is real, how should we build around it?"
```

Or:

```text
Paper 1 = discover the map
Paper 2 = design the machine using the map
```

---

## 12. Takeaway

> Paper 1 establishes whether ASCENT's substrate-control view has empirical
> support in adaptation geometry. Paper 2 turns that result, if positive, into
> an architecture program. The split is not cosmetic; it is what keeps the
> framework scientifically disciplined.
