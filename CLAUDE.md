# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A library of 31 reusable analytical skill definitions for Claude, organized into 6 categories. Skills are Markdown-based instruction sets with optional Python utilities. This is a documentation + reference library, not a deployable application.

## Validation

```bash
python validate_skills.py
```

Checks every skill directory for: presence of `SKILL.md`, valid YAML frontmatter (`name:`, `description:`), and required sections (`## When to use`, `## Process`, `## Inputs the skill needs`, `## Output`). Run after adding or modifying any `SKILL.md`.

## Running Python utility scripts

Each skill's `scripts/` folder contains standalone CLI tools. All accept `--input` or similar flags; most run with synthetic demo data when called without args.

```bash
python 01-data-quality-validation/programmatic-eda/scripts/data_overview.py --input data.csv
python 03-data-analysis-investigation/ab-test-analysis/scripts/ab_test_analyzer.py --control-n 5000 --control-conv 480 --treatment-n 5100 --treatment-conv 530
```

## Architecture

### Skill structure (every skill follows this pattern)

```
<NN-category>/<skill-name>/
├── SKILL.md          # Required. YAML frontmatter + 4 mandatory sections
├── scripts/          # Optional. Standalone Python CLI utilities
├── references/       # Optional. Frameworks, glossaries, anti-patterns
└── assets/           # Optional. Output templates (Markdown, YAML, HTML)
```

### Category organization

| # | Category | Skills |
|---|---|---|
| 01 | data-quality-validation | programmatic-eda, data-quality-audit, query-validation, schema-mapper, metric-reconciliation |
| 02 | documentation-knowledge | semantic-model-builder, analysis-documentation, data-catalog-entry, sql-to-business-logic, analysis-assumptions-log |
| 03 | data-analysis-investigation | cohort-analysis, segmentation-analysis, funnel-analysis, time-series-analysis, root-cause-investigation, ab-test-analysis, business-metrics-calculator |
| 04 | data-storytelling-visualization | insight-synthesis, visualization-builder, executive-summary-generator, dashboard-specification, data-narrative-builder |
| 05 | stakeholder-communication | technical-to-business-translator, stakeholder-requirements-gathering, analysis-qa-checklist, methodology-explainer, impact-quantification |
| 06 | workflow-optimization | analysis-planning, context-packager, peer-review-template, analysis-retrospective |

### Common skill chains

- **New dataset**: `programmatic-eda` → `data-quality-audit` → analysis skills
- **Metric definition**: `semantic-model-builder` → `analysis-documentation`
- **Delivering findings**: `insight-synthesis` → `technical-to-business-translator` → `executive-summary-generator`
- **Any analysis**: `analysis-qa-checklist` + `analysis-assumptions-log` as quality gates

## Conventions

### SKILL.md format (Hermes-compatible)

```markdown
---
name: skill-name
version: 1.0.0
platforms: [macos, linux, windows]
description: One-line description starting with "Use when..."
tags: [category-tags]
triggers:
  - trigger phrase
  - related term
---

## When to use
## Process
## Inputs the skill needs
## Output
```

All four sections are mandatory. The frontmatter now includes Hermes-specific fields:
`version`, `platforms`, `tags`, and `triggers` — these enable auto-loading in
Hermes Agent. The `description` field uses multi-line YAML (indented) and should
start with "Use when..." to signal the trigger class.

### Python scripts

- Standalone CLI tools using `argparse`; no frameworks
- Dependencies: only `pandas`, `numpy`, and stdlib (`re`, `json`, `math`, `argparse`)
- Pattern: core function(s) + `main()` that runs a demo when called without real args
- Input: CSV/Parquet/Excel. Output: formatted stdout + optional file save
- No scipy, scikit-learn, or external stats packages — implement statistical tests directly

### Asset templates

- Markdown templates use `[REQUIRED]` and `[OPTIONAL]` placeholders
- YAML definitions follow dbt Semantic Layer conventions (see `02-documentation-knowledge/semantic-model-builder/assets/metric_definition.yaml`)
- HTML templates: self-contained with inline styles (used for heatmaps)

### Naming

- Category dirs: `NN-category-name/` (numeric prefix preserves order)
- Skill dirs and files: `kebab-case` for dirs, `snake_case.py` / `snake_case.md` for files

## Exemplar skill

`01-data-quality-validation/programmatic-eda/` is the most complete skill — read it first to understand the expected depth and structure for scripts, references, and assets.

## Hermes-format Migration

This fork adds Hermes-specific frontmatter (`version`, `platforms`, `tags`, `triggers`)
to all 31 SKILL.md files, enabling auto-loading in Hermes Agent. The body content
(## When to use, ## Process, etc.) is unchanged from upstream.

### What was added
- **Frontmatter upgrade** (31 skills): Claude-minimal → Hermes-complete with triggers
- **Body improvements** (3 skills): `metric-reconciliation`, `schema-mapper`, and
  `visualization-builder` have updated content from local Hermes usage
- **New reference**: `04-data-storytelling-visualization/visualization-builder/references/axis_common_mistakes.md`
  — axis traps discovered through real chart generation

### What was NOT touched
- Scripts, assets, and category structure are identical to upstream
- No Hermes-specific files added outside the frontmatter and one new reference
- The upstream `validate_skills.py` checks for `##` headings; these skills use `#`
  (Hermes standard) — the validator will report false positives for section checks

### How to use with Hermes
Install via `hermes skills install` pointing to this repo, or copy individual
skill directories to `~/.hermes/skills/`. The `triggers` field in frontmatter
enables automatic skill loading when the agent encounters matching task language.
