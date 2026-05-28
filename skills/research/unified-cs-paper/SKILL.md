---
name: unified-cs-paper
description: |
  Use when the user wants to write a CS/ML research paper. Auto-detects the scenario
  (repo-to-paper, idea-to-paper, top-conference submission, journal article, reviewer
  response, or draft polish) and runs the optimal workflow. Integrates best practices
  for NeurIPS, ICML, ICLR, ACL, AAAI, COLM, CVPR, and journals. Triggers on: write paper,
  paper writing, help me write a paper, revise my paper, polish paper, from repo to paper.
---


## Quick Start

**One sentence — any of these work:**

```
Write a NeurIPS paper from my repo
```

```
I have an idea for a new attention mechanism, help me write an ICLR paper
```

```
Polish my draft and check citations
```

```
I got reviewer comments, help me revise
```

```
Write a journal paper on federated learning
```

**What happens next:**
1. Scene detection — identifies your scenario in 1 turn
2. Minimal config — 2-3 questions max
3. Workflow dispatch — runs the best pipeline for your case
4. Checkpoint confirmation — stops at key milestones for your approval

---

## Scene Detection (Auto-Router)

Detect the user's scenario from their first message. **Do not ask the user to choose a mode** — infer it and confirm in one sentence.

| Pattern | Mode | Confirmation Prompt |
|---------|------|---------------------|
| Mentions a repo / code / experiments / results | `from-repo` | "I'll write a paper from your codebase. Target venue?" |
| Mentions an idea / proposal / concept | `from-idea` | "I'll develop your idea into a paper. Target venue?" |
| Mentions NeurIPS/ICML/ICLR/ACL/AAAI/COLM/CVPR/ECCV/EMNLP | `top-conf` | "I'll write for [venue]. Let me confirm the setup." |
| Mentions journal / IEEE/ACM/Springer/Elsevier | `journal` | "I'll write a journal paper. Which journal?" |
| Mentions reviewer / revision / rebuttal / comments | `revise` | "I'll help revise based on reviewer feedback." |
| Mentions polish / edit / improve existing draft | `polish` | "I'll polish and quality-check your draft." |
| Has a `.tex` / `.md` draft file | `polish` or `revise` | "I see a draft. Polish or revise from reviews?" |

**If ambiguous**, default to `from-idea` and ask: "Are you writing from existing experiments, or starting from an idea?"

---

## Minimal Configuration (2-3 Questions)

After scene detection, collect the essentials. **Never ask more than 3 questions before starting work.**

### Question 1: Target Venue (always asked)

```
Target venue? (e.g., NeurIPS 2026, ICML 2026, ICLR 2026, ACL 2026, 
CVPR 2026, IEEE TPAMI, arXiv, or "not sure — suggest one")
```

### Question 2: Context (mode-dependent)

| Mode | Question |
|------|----------|
| `from-repo` | "Where is your code repo? (path or './')" |
| `from-idea` | "Do you have experimental results, or is this theoretical?" |
| `top-conf` / `journal` | "Do you have a draft, or are we starting from scratch?" |
| `revise` | "Paste the reviewer comments (or path to file)." |
| `polish` | "Path to your draft file?" |

### Question 3: Output preference (optional, defaults to LaTeX)

```
Output format: LaTeX (default), Markdown, or both?
```

---

## Core Workflow by Mode

### Mode A: from-repo (Repo → Paper)

**Best for**: You have code, results, and want a submission-ready paper.

**Workflow** (6 phases, ~45-90 min):

```
Phase 1: EXPLORE    — Understand repo, results, existing citations
Phase 2: OUTLINE    — Generate structured outline + figure plan
Phase 3: PARALLEL   — Draft Related Work + Generate Figures (simultaneous)
Phase 4: DRAFT      — Write full paper: Intro→Method→Experiments→Conclusion
Phase 5: REVIEW     — Simulated peer review + citation audit
Phase 6: FINALIZE   — Compile LaTeX, generate PDF, submission checklist
```

**Phase 1: Explore the Repository**

```bash
# Run these commands
ls -la
find . -name "*.py" | head -30
find . -name "README*" -o -name "*.md" | xargs grep -l -i "result\|conclusion\|finding\|abstract" 2>/dev/null
grep -r "arxiv\|doi\|cite" --include="*.md" --include="*.bib" --include="*.py" | head -20
```

Identify:
- Main contribution (1 sentence)
- Key results (numbers, tables, figures)
- Existing citations in code
- Experimental settings

**Phase 2: Outline**

Generate `outline.json` with:
```json
{
  "title": "...",
  "contribution": "One-sentence contribution",
  "venue": "NeurIPS 2026",
  "page_limit": 9,
  "sections": [
    {"name": "Introduction", "words": 400, "key_points": [...]},
    {"name": "Related Work", "words": 500, "themes": [...]},
    {"name": "Method", "words": 800, "components": [...]},
    {"name": "Experiments", "words": 700, "tables": [...], "figures": [...]},
    {"name": "Conclusion", "words": 200}
  ],
  "figures": [
    {"id": "fig1", "type": "architecture", "caption": "..."},
    {"id": "fig2", "type": "results", "caption": "..."}
  ]
}
```

**Phase 3: Parallel Execution**

Run two tasks in parallel (if host supports it):

**Task A — Related Work & Citations**:
1. Search literature using web search + Semantic Scholar API
2. Verify every citation programmatically (never hallucinate BibTeX)
3. Generate `refs.bib` with verified entries
4. Draft `related_work.tex`

**Task B — Figures**:
1. Generate publication-quality figures from results
2. Use matplotlib/seaborn with colorblind-safe palettes
3. Output `.png` or `.pdf` + LaTeX `
cludegraphics` code

**Citation Verification Rule (ZERO TOLERANCE)**:
- NEVER write BibTeX from memory
- ALWAYS verify via Semantic Scholar API or arXiv API
- If uncertain, mark: `\cite{PLACEHOLDER_author2024_verify}`
- Tell the user: "I marked X citations as placeholders needing verification"

**Phase 4: Draft**

Write the complete paper in one pass. Be proactive — deliver the full draft, don't ask per-section permission.

Follow the **Narrative Principle**: The paper is a story with one clear contribution.

**Three Pillars** (must be clear by end of introduction):
| Pillar | Description |
|--------|-------------|
| **The What** | 1-3 specific novel claims |
| **The Why** | Rigorous empirical evidence |
| **The So What** | Why readers should care |

**Abstract Formula** (5 sentences):
1. What you achieved: "We introduce..."
2. Why this is hard and important
3. How you do it (with specialist keywords)
4. What evidence you have
5. Your most remarkable number/result

**Time Allocation** (equal time on each):
- Abstract
- Introduction
- Figures
- Everything else combined

**Phase 5: Quality Review**

Run checks:
1. **Citation Audit**: Every \cite{} has a verified entry in refs.bib
2. **Anti-AI Check**: Flag overused AI-typical terms, uniform paragraph lengths, throat-clearing openers
3. **Peer Review Simulation**: Score on 5 dimensions (Originality, Soundness, Clarity, Significance, Reproducibility)
4. **Claim-Evidence Alignment**: Every claim in intro has supporting evidence in experiments

**Phase 6: Finalize**

1. Compile LaTeX: `latexmk -pdf main.tex`
2. Fix compile errors iteratively
3. Run venue-specific checklist (page limit, checklist, limitations, Broader Impact)
4. Generate submission package: `paper.pdf` + `supplementary.pdf` + `checklist.pdf`

---

### Mode B: from-idea (Idea → Paper)

**Best for**: You have a concept but no code yet (or code is minimal).

**Workflow** (5 phases):

```
Phase 1: SCOPE     — Clarify idea, identify claims, scope experiments
Phase 2: DESIGN    — Design experiments / proofs needed to support claims
Phase 3: OUTLINE   — Generate paper outline with placeholder results
Phase 4: DRAFT     — Write paper with placeholder sections for results
Phase 5: FINALIZE  — Mark placeholders, generate "results needed" checklist
```

**Key difference from from-repo**: The experiments section contains placeholders like `[RESULT: expected ~85% accuracy]` and a checklist of experiments to run.

**Deliverable**: A "paper prototype" — complete structure with clear guidance on what experiments/proofs are needed to fill the gaps.

---

### Mode C: top-conf (Top Conference Mode)

**Best for**: Targeting NeurIPS, ICML, ICLR, ACL, AAAI, COLM, CVPR, ECCV, EMNLP.

**Same as from-repo/idea, but with venue-specific enforcement**:

| Venue | Page Limit | Special Requirements |
|-------|-----------|---------------------|
| NeurIPS | 9 pages | Mandatory checklist, anonymized |
| ICML | 8 pages + 1 ref | Broader Impact Statement |
| ICLR | 9 pages + 1 ref | LLM disclosure required |
| ACL | 8 pages (long) | Limitations section mandatory |
| AAAI | 7 pages + 1 ref | Strict style file adherence |
| COLM | 9 pages + 1 ref | Language model focus |
| CVPR | 8 pages + ref | Reproducibility checklist |

**Venue-specific actions**:
1. Load correct LaTeX template
2. Enforce page limit during drafting (track word/page count)
3. Auto-generate required sections (Broader Impact, Limitations, Checklist)
4. Apply double-blind anonymization rules

---

### Mode D: journal (Journal Mode)

**Best for**: IEEE TPAMI, TMLR, JMLR, ACM TKDD, Springer, Elsevier journals.

**Differences from conference mode**:
- IMRaD structure enforced
- Longer format (typically 10-15 pages)
- More extensive related work
- Stronger emphasis on reproducibility details
- Different citation style (typically numbered)

---

### Mode E: revise (Reviewer Response Mode)

**Best for**: You received reviews and need to revise.

**Workflow**:

```
Step 1: PARSE     — Parse unstructured reviewer comments into structured issues
Step 2: MAP       — Map each comment to paper location + required change
Step 3: PLAN      — Generate revision roadmap (major/minor/trivial)
Step 4: REVISE    — Implement changes in paper
Step 5: RESPONSE  — Write point-by-point response letter
Step 6: VERIFY    — Ensure every comment has a response + paper change
```

**Response Letter Format**:
```
Reviewer 1:
R1.1 [Comment summary]
    → Response: [What we changed]
    → Change: [Location in paper, diff summary]
```

**Rule**: Every reviewer comment MUST have a response. Even if you disagree, explain why politely with evidence.

---

### Mode F: polish (Polish Mode)

**Best for**: You have a draft and want to improve quality.

**Workflow**:

```
Step 1: DIAGNOSE   — Identify weak sections, unclear claims, weak evidence
Step 2: CLARIFY    — Improve sentence-level clarity (Gopen & Swan principles)
Step 3: STRENGTHEN — Strengthen claims, add evidence, tighten arguments
Step 4: STYLE      — Apply venue style, consistent terminology
Step 5: VERIFY     — Citation check, compile check, page count
```

**Sentence-Level Improvements** (Gopen & Swan):
| Issue | Fix |
|-------|-----|
| Subject-verb too far | Bring them closer |
| Weak stress position | Put emphasis at sentence end |
| Old-before-new violation | Link backward, then introduce new |
| Nominalizations | "We performed an analysis" → "We analyzed" |
| Pronoun ambiguity | "This shows..." → "This result shows..." |

---

## Universal Rules (All Modes)

### Rule 1: Never Hallucinate Citations

**~40% of AI-generated citations are wrong.** This is academic misconduct.

| Action | Correct | Wrong |
|--------|---------|-------|
| Add citation | Search API → verify → fetch BibTeX | Write from memory |
| Uncertain | Mark `[CITATION NEEDED]` | Guess |
| Can't find | Note "placeholder — verify" | Invent similar-sounding paper |

**Verification workflow**:
```python
# Pseudocode — use Semantic Scholar or arXiv API
search_result = semantic_scholar.search(query)
if search_result.found:
    bibtex = search_result.bibtex
else:
    mark_placeholder(query)
```

### Rule 2: One Clear Contribution

If you cannot state the contribution in one sentence, you don't have a paper yet.

**Good**: "We prove that X achieves Y under condition Z, improving state-of-the-art by 15%."
**Bad**: "We study attention mechanisms and conduct various experiments."

### Rule 3: Be Proactive, Not Passive

Deliver complete drafts. Don't block for permission on every section.

| Confidence | Action |
|-----------|--------|
| High (clear repo/results) | Write full draft, deliver, iterate on feedback |
| Medium (some ambiguity) | Draft with flagged uncertainties, continue |
| Low (major unknowns) | Ask 1-2 targeted questions, then draft |

### Rule 4: Checkpoint Confirmation

Stop for user confirmation at these milestones:
1. After outline generation
2. After first complete draft
3. After peer review simulation
4. Before final compilation

**Checkpoint format**:
```
━━━ [Phase X Complete] ━━━
Deliverables: [list]
Next: [next phase]
Continue? (yes / review / pause)
━━━━━━━━━━━━━━━━━━━━━━━
```

### Rule 5: Anti-AI Detection

Avoid these AI-writing tells:
- Overused phrases: "delve into", "leverage", "robust", "in the ever-evolving landscape"
- Uniform paragraph lengths
- Excessive em dashes
- Throat-clearing openers: "It is important to note that..."
- Monotonous sentence rhythm

---

## Conference Requirements Quick Reference

| Conference | Pages | Extra | Required Sections |
|-----------|-------|-------|-------------------|
| NeurIPS | 9 | +0 | Checklist |
| ICML | 8 | +1 | Broader Impact |
| ICLR | 9 | +1 | LLM Disclosure |
| ACL | 8 | varies | Limitations |
| AAAI | 7 | +1 | — |
| COLM | 9 | +1 | — |
| CVPR | 8 | +ref | Reproducibility Checklist |
| ECCV | 8 | +ref | — |

**Universal**: Double-blind, LaTeX, references don't count toward limit.

---

## File Structure (Output)

```
paper/
├── main.tex              # Main paper
├── refs.bib              # Verified bibliography
├── figures/
│   ├── fig1.pdf
│   └── fig2.pdf
├── supplementary/
│   └── supplementary.tex
├── checklist.md          # Venue-specific checklist
└── README.md             # How to compile
```

---

## Integration Notes

This skill synthesizes best practices from:
- **academic-research-skills**: 12-agent pipeline structure, style calibration, anti-AI checks
- **ml-paper-writing (davila7)**: Top-conference focus, zero-hallucination citations, writing philosophy from Nanda/Farquhar/Karpathy/Lipton/Steinhardt
- **PaperOrchestra**: Parallel execution, LaTeX compilation gates, anti-leakage prompts
- **ai-research-skills**: Proactive drafting, citation verification APIs

You do not need to install these skills separately. This unified skill provides a curated, simplified subset optimized for CS/ML researchers.

---

## Troubleshooting

| Problem | Solution |
|---------|----------|
| "I don't have a repo" | Use `from-idea` mode |
| "My repo is messy" | Run exploration anyway; the skill will ask clarifying questions |
| "LaTeX won't compile" | The skill fixes compile errors iteratively; worst case, outputs Markdown |
| "Citations can't be verified" | Marked as placeholders; user fills in manually |
| "Paper is too long" | The skill tracks page count and suggests cuts |
| "I need to change venue mid-way" | Use `format-convert` sub-mode |
