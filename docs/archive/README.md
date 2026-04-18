# Archive: operator-coordinate-compression

This directory preserves key documents from the predecessor project,  
`operator-coordinate-compression` (github.com/che-yjwj/operator-coordinate-compression).

That project investigated LLM weight compression through coordinate system selection.  
Its core insight — that outliers are coordinate artifacts and compression is a coordinate choice  
problem — is preserved and evolved in the ASCENT framework.

---

## Why Archived

The original project's framing was:
> "LLM compression is about finding the right coordinate system for operator parameters."

ASCENT evolves this to:
> "LLM adaptation is primarily a control problem over a pretrained capability substrate."

The coordinate-system insight is now a sub-component of ASCENT's theoretical foundation  
(specifically: why rotation-based quantization works, and why it isn't enough).

---

## Key Documents to Transfer Here

From `operator-coordinate-compression`, the most valuable documents are:

| File | Value | ASCENT Mapping |
|------|-------|----------------|
| `docs/theory/turboquant_llm_quantization_architecture.md` | TurboQuant analysis with FACT/INFERENCE/PLAN discipline | Evidence mapping §2.5 |
| `docs/theory/operator_view.md` | Weights-as-operators framing | Framework §3 motivation |
| `docs/theory/rotation_vs_alignment.md` | Flattening vs concentration distinction | Evidence mapping |
| `docs/research_direction_review.md` | Self-critique of original framework | Evolution document |
| `docs/patent/invention_summary.md` | Patent claims draft | Patent work (M15-22) |
| `docs/patent/prior_art_mapping.md` | Prior art analysis | Evidence mapping |
| `docs/patent/claim_candidates.md` | Claim candidates | Patent work |

---

## Instructions

To populate this directory:

```bash
# Clone old repo
git clone https://github.com/che-yjwj/operator-coordinate-compression old-repo

# Copy key documents
cp old-repo/docs/theory/turboquant_llm_quantization_architecture.md docs/archive/operator-coordinate-compression/
cp old-repo/docs/theory/operator_view.md docs/archive/operator-coordinate-compression/
cp old-repo/docs/theory/rotation_vs_alignment.md docs/archive/operator-coordinate-compression/
cp old-repo/docs/research_direction_review.md docs/archive/operator-coordinate-compression/
cp old-repo/docs/patent/*.md docs/archive/operator-coordinate-compression/patent/

# Then archive the old repo via GitHub Settings
```

After copying, the old repo should be archived (read-only) on GitHub.

---

*These documents are preserved for historical and intellectual continuity.  
They represent the research lineage that led to ASCENT.*
