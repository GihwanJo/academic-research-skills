---
name: academic-research-skills
description: "Academic research skill suite (v3.6.5). 4 skills, 25 modes. Routes to: deep-research v2.9.2 (13-agent research team), academic-paper v3.1.1 (12-agent paper writing), academic-paper-reviewer v1.9.0 (5-reviewer peer review with Sprint Contract hard gate), academic-pipeline v3.6.5 (10-stage orchestrator with Material Passport reset boundary + literature_corpus[] input port). Triggers: research, deep research, literature review, systematic review, meta-analysis, fact-check, guide my research, write paper, academic paper, paper outline, revise paper, parse reviews, AI disclosure, review paper, peer review, manuscript review, academic pipeline, research to paper, full paper workflow, resume from passport, 연구, 심층 연구, 문헌 검토, 체계적 문헌 검토, 메타 분석, 사실 확인, 연구 지도, 내용 명확화 지원, 논문 작성, 학술 논문 작성 지도, 논문 검토 및 의견 제공."
metadata:
  version: "3.6.5"
  last_updated: "2026-04-27"
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
| Research a topic, literature review, fact-check, systematic review | **deep-research** | v2.9.2 | `deep-research/SKILL.md` |
| Write a paper, outline, abstract, revise, format-convert, citation-check, AI disclosure | **academic-paper** | v3.1.1 | `academic-paper/SKILL.md` |
| Review/critique a paper, re-review revisions, calibrate reviewer | **academic-paper-reviewer** | v1.9.0 | `academic-paper-reviewer/SKILL.md` |
| Full end-to-end pipeline (research -> write -> review -> revise -> finalize) | **academic-pipeline** | v3.6.5 | `academic-pipeline/SKILL.md` |

Suite version: **v3.6.5**. See `CHANGELOG.md` for full version history.

## Routing Rules

1. **Pipeline vs single skill**: If the user needs only ONE function (just research, just write, just review), dispatch to that skill directly. Only use `academic-pipeline` when the user wants the full end-to-end workflow.

2. **Unclear research question**: Suggest `deep-research` in `socratic` mode to help clarify.

3. **Wants to plan paper structure**: Suggest `academic-paper` in `plan` mode.

4. **Wants to learn from review**: Suggest `academic-paper-reviewer` in `guided` mode.

5. **Resuming a prior pipeline run**: If the user references a Material Passport hash or asks to continue from a reset boundary, dispatch to `academic-pipeline` with `resume_from_passport=<hash>` (requires `ARS_PASSPORT_RESET=1`). See `academic-pipeline/references/passport_as_reset_boundary.md`.

6. **Default flow** when pipeline is requested:
   ```
   deep-research (socratic/full)
     -> academic-paper (plan/full)
       -> academic-paper-reviewer (full/guided)
         -> academic-paper (revision)
           -> academic-paper-reviewer (re-review, max 2 loops)
             -> academic-paper (format-convert -> final output)
             -> AI Self-Reflection Report
   ```

## Mode Quick Reference (25 modes)

See `MODE_REGISTRY.md` for the full registry with triggers and output types.

### deep-research (7 modes)
`full` | `quick` | `socratic` | `review` | `lit-review` | `fact-check` | `systematic-review`

### academic-paper (10 modes)
`full` | `plan` | `outline-only` | `revision` | `revision-coach` | `abstract-only` | `lit-review` | `format-convert` | `citation-check` | `disclosure`

### academic-paper-reviewer (6 modes)
`full` | `re-review` | `quick` | `methodology-focus` | `guided` | `calibration`

### academic-pipeline (1 orchestrator + 1 resume mode)
10-stage workflow with mandatory integrity verification and two-stage peer review. `resume_from_passport=<hash>` resumes from a Material Passport reset boundary in a fresh session.

## Handoff Protocol

### deep-research -> academic-paper
Materials: RQ Brief, Methodology Blueprint, Annotated Bibliography, Synthesis Report, INSIGHT Collection. When the Material Passport carries a non-empty `literature_corpus[]`, the corpus-first / search-fills-gap flow engages automatically (see `academic-pipeline/references/literature_corpus_consumers.md`).

### academic-paper -> academic-paper-reviewer
Materials: Complete paper text. `field_analyst_agent` auto-detects domain and configures reviewers. Reviewer runs paper-content-blind Phase 1 + paper-visible Phase 2 under the Sprint Contract hard gate (Schema 13).

### academic-paper-reviewer -> academic-paper (revision)
Materials: Editorial Decision Letter, Revision Roadmap, Per-reviewer detailed comments

## Shared Resources

| Resource | Path |
|---|---|
| Cross-model verification | `shared/cross_model_verification.md` |
| Handoff schemas (13 schemas incl. compliance_report, sprint_contract) | `shared/handoff_schemas.md` |
| Mode spectrum | `shared/mode_spectrum.md` |
| Style calibration | `shared/style_calibration_protocol.md` |
| Artifact reproducibility | `shared/artifact_reproducibility_pattern.md` |
| Benchmark report schema | `shared/benchmark_report.schema.json` |
| Benchmark report pattern | `shared/benchmark_report_pattern.md` |
| Ground-truth isolation | `shared/ground_truth_isolation_pattern.md` |
| Compliance agent (v3.4) — PRISMA-trAIce 17 items + RAISE 4 principles + 8-role matrix | `shared/agents/compliance_agent.md` |
| Compliance checkpoint protocol (v3.4) — 3-round override ladder + disclosure_addendum | `shared/compliance_checkpoint_protocol.md` |
| Collaboration depth rubric (v3.5) — 4-dimension user-AI collaboration scoring | `shared/collaboration_depth_rubric.md` |
| **Sprint contract schema (v3.6.2)** — reviewer hard-gate acceptance criteria | `shared/sprint_contract.schema.json` |
| **Reviewer contract templates (v3.6.2)** — `full.json` (panel 5), `methodology_focus.json` (panel 2) | `shared/contracts/reviewer/` |
| **Passport contracts (v3.6.3+)** — reset_ledger_entry, literature_corpus_entry, rejection_log schemas | `shared/contracts/passport/` |
| PRISMA-trAIce protocol | `shared/prisma_trAIce_protocol.md` |
| RAISE framework | `shared/raise_framework.md` |

## Version History

See `CHANGELOG.md` for the authoritative changelog. Per-skill changelogs live at `<skill>/references/changelog.md`.

## Key Rules

- All claims must have citations
- Evidence hierarchy: meta-analyses > RCTs > cohort > case reports > expert opinion
- Contradictions disclosed with evidence quality comparison
- AI disclosure in all reports (any compliance override auto-appends a `disclosure_addendum`)
- Compliance gates (PRISMA-trAIce + RAISE + 8-role matrix) run at Stage 2.5 and Stage 4.5; tier-based block with 3-round override ladder
- Sprint Contract (Schema 13) is the reviewer hard gate for `full` and `methodology_focus`; reviewer runs Phase 1 paper-content-blind, Phase 2 paper-visible
- When the Material Passport carries `literature_corpus[]`, Phase 1 consumers run corpus-first / search-fills-gap; the four Iron Rules (Same criteria / No silent skip / No corpus mutation / Graceful fallback) hold
- Output language matches user input (Korean or English; Traditional Chinese supported in bilingual abstract mode)
