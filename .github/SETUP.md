# SETUP.md - GitHub Organization Setup Guide

Historical bootstrap guide for the initial ASCENT repository setup.
Some steps below reflect the April 2026 bootstrapping state and are preserved as project record rather than current operational truth.

---

## Step 1: Create GitHub Organization (5 minutes)

1. Go to: `github.com` -> your profile -> `+` -> New organization
2. Organization name: `ascent-framework` (or `ascent-research` if taken)
3. Plan: Free
4. Contact email: your email
5. Skip team setup for now (solo researcher)

**Result:** `github.com/ascent-framework/`

---

## Step 2: Create Three Repositories (10 minutes)

### Repo 1: ascent-framework (this repo; main docs)

```
Name:        ascent-framework
Description: ASCENT Framework: Adaptation as Selection on Capability Networks and Topology
Visibility:  Public
Initialize:  Yes (add README)
.gitignore:  Python
License:     MIT
```

### Repo 2: ascent-geometry (Paper 1 experiments)

```
Name:        ascent-geometry
Description: ASCENT-G: Adaptation Geometry experiments (Paper 1)
Visibility:  Public
Initialize:  Yes
.gitignore:  Python
License:     MIT
```

### Repo 3: ascent-arch (Paper 2; create now, populate later)

```
Name:        ascent-arch
Description: ASCENT-A: Small Controllers over Structured Capabilities (Paper 2; planned)
Visibility:  Public
Initialize:  Yes
.gitignore:  Python
License:     MIT
Note:        Add README saying "Coming M13+"
```

---

## Step 3: Set Up ascent-framework (this repo)

### Clone and add files

```bash
git clone https://github.com/ascent-framework/ascent-framework.git
cd ascent-framework

# Create directory structure
mkdir -p docs/{framework,preregistration,paper1,exploration,future-directions,archive,archive/operator-coordinate-compression}

# Copy files from generated documents
cp [downloaded files] .

# First commit
git add .
git commit -m "Initial framework documents: v6.1, preregistration v1.2, roadmap"
git push origin main
```

### Recommended branch structure

```
main          stable documents
dev           active work, drafts
paper1        Paper 1 specific work (create M11)
```

---

## Step 4: Archive Previous Repo

Transfer key documents from `operator-coordinate-compression` to `ascent-framework/docs/archive/`:

```bash
# Files worth keeping:
docs/theory/turboquant_llm_quantization_architecture.md  -> archive/operator-coordinate-compression/
docs/theory/operator_view.md                             -> archive/operator-coordinate-compression/
docs/research_direction_review.md                        -> archive/operator-coordinate-compression/
docs/patent/invention_summary.md                         -> archive/operator-coordinate-compression/
docs/patent/prior_art_mapping.md                         -> archive/operator-coordinate-compression/
docs/patent/claim_candidates.md                          -> archive/operator-coordinate-compression/

# Then archive the old repo:
# GitHub Settings -> Danger Zone -> Archive this repository
```

Add `docs/archive/README.md` explaining what's there and why.

Current archive destination: `docs/archive/operator-coordinate-compression/`.

---

## Step 5: Connect OSF to GitHub

1. Create OSF account: `osf.io`
2. Create new project: `ASCENT Framework`
3. Add description (copy from `README.md` introduction)
4. Add-ons -> GitHub -> connect `ascent-framework` repo
5. Upload the current pre-registration version as a file (PDF preferred)
6. Register: Create Registration -> Open-Ended Registration
7. Wait 24-48h for public URL

**OSF URL format:** `osf.io/[5-character-code]`

Add this URL to:

- `docs/preregistration/v1.3.md` (top of file)
- `README.md` (pre-registration badge)

---

## Step 6: Add GitHub Badges to README

Add these to the top of `README.md` (after the title):

```markdown
[![OSF Pre-registration](https://img.shields.io/badge/OSF-Pre--registered-blue)](https://osf.io/YOUR_URL)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Status: Active](https://img.shields.io/badge/Status-Active-green)]()
```

---

## Step 7: Set Up ascent-geometry for Experiments

```bash
git clone https://github.com/ascent-framework/ascent-geometry.git
cd ascent-geometry

# Create structure
mkdir -p src/{training,extraction,analysis,statistics,viz}
mkdir -p data/{raw,vectors,results}
mkdir -p notebooks
mkdir -p scripts

# Key files to create (Phase 0, Week 5-6):
touch src/training/trainer.py        # TinyLoRA + GRPO
touch src/extraction/vectors.py      # update vector extraction
touch src/analysis/svd_analysis.py   # H1a analysis
touch src/analysis/projection.py     # H1b analysis
touch src/statistics/bootstrap.py    # CI computation
touch src/viz/plots.py               # matplotlib/seaborn figures
touch scripts/run_h1a.sh             # end-to-end H1a script
touch requirements.txt
touch README.md
```

### requirements.txt (starter)

```
torch>=2.1.0
transformers>=4.40.0
peft>=0.10.0
trl>=0.8.0
datasets>=2.18.0
numpy>=1.24.0
scipy>=1.11.0
matplotlib>=3.7.0
seaborn>=0.12.0
scikit-learn>=1.3.0
wandb>=0.16.0
```

---

## Step 8: Weekly Workflow

Once set up, your weekly workflow:

```
Monday/Tuesday evening (~2h):
  - Run/monitor training experiments (ascent-geometry)
  - Commit results

Weekend (~3.5h):
  - Analysis and writing
  - Update docs (ascent-framework)
  - Weekly commit + progress note

Monthly (1st of month):
  - Milestone check
  - Update ROADMAP.md with current status
  - Blog post (if milestone reached)
```

### Git hygiene

```bash
# Start each session:
git pull
git checkout -b feature/[task-name]

# End each session:
git add -p
git commit -m "descriptive message"
git push

# Weekly: merge to main
git checkout main
git merge feature/[task-name]
git push
```

---

## Step 9: One-time Environment Setup (RTX 4090)

```bash
# Check CUDA
nvidia-smi
nvcc --version  # target: CUDA 12.1+

# Install
conda create -n ascent python=3.10
conda activate ascent
pip install -r requirements.txt

# Verify Qwen2.5-1.5B loads on 4090
python -c "
from transformers import AutoModelForCausalLM, AutoTokenizer
model = AutoModelForCausalLM.from_pretrained(
    'Qwen/Qwen2.5-1.5B-Instruct',
    device_map='auto',
    torch_dtype='auto'
)
print(model.get_memory_footprint())
"
# Target: < 5GB (leaves 19GB for GRPO)
```

---

## Checklist: Phase 0 Week 1-2 Complete

```
[ ] GitHub organization created: github.com/ascent-framework/
[ ] 3 repos created (ascent-framework, ascent-geometry, ascent-arch)
[ ] ascent-framework populated with 6 documents
[ ] Old repo archived (operator-coordinate-compression)
[ ] OSF account created
[ ] Current pre-registration uploaded to OSF
[ ] ascent-geometry structure created
[ ] requirements.txt installed
[ ] Qwen2.5-1.5B loads successfully on RTX 4090
[ ] First pilot training run attempted (GSM8K, 100 steps)
```

When all boxes are checked: **Phase 0 Week 1-2 complete. Begin Week 3 (TinyLoRA reproduction).**
