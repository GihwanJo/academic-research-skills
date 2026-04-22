---
name: academic-research-skills
description: "Academic research skill suite (v3.3.6). 4 skills, 24 modes. Routes to: deep-research (13-agent research team), academic-paper (12-agent paper writing), academic-paper-reviewer (5-reviewer peer review), academic-pipeline (10-stage orchestrator). Triggers: research, deep research, literature review, systematic review, meta-analysis, fact-check, guide my research, write paper, academic paper, paper outline, revise paper, parse reviews, AI disclosure, review paper, peer review, manuscript review, academic pipeline, research to paper, full paper workflow, 연구, 심층 연구, 문헌 검토, 체계적 문헌 검토, 메타 분석, 사실 확인, 연구 지도, 내용 명확화 지원, 논문 작성, 학술 논문 작성 지도, 논문 검토 및 의견 제공."
metadata:
  version: "3.3.6"
  last_updated: "2026-04-15"
  author: "Cheng-I Wu"
  license: "CC-BY-NC 4.0"
  status: active
  data_access_level: verified_only
  task_type: open-ended
---

# Academic Research Skills — Suite Router

This is the entry point for a 4-skill academic research suite. **Do not execute research/writing here** — read the appropriate sub-skill SKILL.md and follow its instructions.

## Dispatch Table

| User intent | Skill to load | Version | Path |
|---|---|---|---|
| Research a topic, literature review, fact-check, systematic review | **deep-research** | v2.9.0 | `deep-research/SKILL.md` |
| Write a paper, outline, abstract, revise, format-convert, citation-check, AI disclosure | **academic-paper** | v3.1.0 | `academic-paper/SKILL.md` |
| Review/critique a paper, re-review revisions, calibrate reviewer | **academic-paper-reviewer** | v1.8.1 | `academic-paper-reviewer/SKILL.md` |
| Full end-to-end pipeline (research -> write -> review -> revise -> finalize) | **academic-pipeline** | v3.3.0 | `academic-pipeline/SKILL.md` |

Suite version: **v3.4** (see `Recent Suite Additions` below).

## Routing Rules

1. **Pipeline vs single skill**: If the user needs only ONE function (just research, just write, just review), dispatch to that skill directly. Only use `academic-pipeline` when the user wants the full end-to-end workflow.

2. **Unclear research question**: Suggest `deep-research` in `socratic` mode to help clarify.

3. **Wants to plan paper structure**: Suggest `academic-paper` in `plan` mode.

4. **Wants to learn from review**: Suggest `academic-paper-reviewer` in `guided` mode.

5. **Default flow** when pipeline is requested:
   ```
   deep-research (socratic/full)
     -> academic-paper (plan/full)
       -> academic-paper-reviewer (full/guided)
         -> academic-paper (revision)
           -> academic-paper-reviewer (re-review, max 2 loops)
             -> academic-paper (format-convert -> final output)
             -> AI Self-Reflection Report
   ```

## Mode Quick Reference (24 modes)

See `MODE_REGISTRY.md` for the full registry with triggers and output types.

### deep-research (7 modes)
`full` | `quick` | `socratic` | `review` | `lit-review` | `fact-check` | `systematic-review`

### academic-paper (10 modes)
`full` | `plan` | `outline-only` | `revision` | `revision-coach` | `abstract-only` | `lit-review` | `format-convert` | `citation-check` | `disclosure`

### academic-paper-reviewer (6 modes)
`full` | `re-review` | `quick` | `methodology-focus` | `guided` | `calibration`

### academic-pipeline (1 orchestrator)
10-stage workflow with mandatory integrity verification and two-stage peer review.

## Handoff Protocol

### deep-research -> academic-paper
Materials: RQ Brief, Methodology Blueprint, Annotated Bibliography, Synthesis Report, INSIGHT Collection

### academic-paper -> academic-paper-reviewer
Materials: Complete paper text. field_analyst_agent auto-detects domain and configures reviewers.

### academic-paper-reviewer -> academic-paper (revision)
Materials: Editorial Decision Letter, Revision Roadmap, Per-reviewer detailed comments

## Shared Resources

| Resource | Path |
|---|---|
| Cross-model verification | `shared/cross_model_verification.md` |
| Handoff schemas (12 schemas incl. compliance_report) | `shared/handoff_schemas.md` |
| Mode spectrum | `shared/mode_spectrum.md` |
| Style calibration | `shared/style_calibration_protocol.md` |
| Artifact reproducibility | `shared/artifact_reproducibility_pattern.md` |
| Benchmark report schema | `shared/benchmark_report.schema.json` |
| Benchmark report pattern | `shared/benchmark_report_pattern.md` |
| Ground-truth isolation | `shared/ground_truth_isolation_pattern.md` |
| **Compliance agent (v3.4)** — PRISMA-trAIce 17 items + RAISE 4 principles + 8-role matrix | `shared/agents/compliance_agent.md` |
| **Compliance checkpoint protocol (v3.4)** — 3-round override ladder + disclosure_addendum | `shared/compliance_checkpoint_protocol.md` |

## Recent Suite Additions

### v3.4 (current)
- **Compliance Agent (shared)**: single mode-aware agent running PRISMA-trAIce 17 items + RAISE 4 principles + 8-role matrix. Hooks Stage 2.5 / 4.5 Integrity Gates with tier-based block. Non-SR entries run principles-only warn-only. See `shared/agents/compliance_agent.md`.
- **Schema 12 `compliance_report`**: append-only audit trail inside Material Passport via `compliance_history[]`. Defined in `shared/handoff_schemas.md`.
- **3-round override ladder**: user overrides at compliance checkpoints produce an auto-injected `disclosure_addendum` attached to the paper's AI-disclosure block. See `shared/compliance_checkpoint_protocol.md`.
- **Long-running session docs**: `docs/PERFORMANCE.md` now covers cross-session resume via Material Passport.

### v3.3
- Semantic Scholar API verification (Tier 0), Anti-Leakage protocol, VLM figure verification, Score Trajectory protocol, Stage 2 parallelization.

### v3.2
- 7-mode AI Research Failure checklist (Lu 2026), Reviewer Calibration mode, Disclosure mode, Early-Stopping + Budget Transparency, Fidelity-Originality mode spectrum.

### v3.0
- Anti-sycophancy protocols (DA rebuttal scoring 1-5, no concession below 4/5), Intent detection (exploratory vs goal-oriented), optional cross-model verification, AI Self-Reflection Report at Stage 6.

## Key Rules

- All claims must have citations
- Evidence hierarchy: meta-analyses > RCTs > cohort > case reports > expert opinion
- Contradictions disclosed with evidence quality comparison
- AI disclosure in all reports (v3.4: any compliance override auto-appends a `disclosure_addendum`)
- Compliance gates (PRISMA-trAIce + RAISE + 8-role matrix) run at Stage 2.5 and Stage 4.5; tier-based block with 3-round override ladder
- Output language matches user input (Korean or English; Traditional Chinese supported in bilingual abstract mode)
