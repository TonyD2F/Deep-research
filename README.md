# Ultimate Deep Research

A production-grade research skill for coding agents. It runs an 8-phase pipeline — scope, parallel retrieval, evidence registry, synthesis, counter-review, mechanical verification — and produces citation-backed Markdown reports instead of confident guesses.

Merged from the strongest open-source research skills (see `references/lineage.md`). Principle: **breadth-first parallel retrieval, evidence on disk, synthesis only from notes, adversarial review, mechanical verification.**

## Why it is different

- **Search, don't recall.** Every factual claim traces to a retrieved source, not model memory.
- **Evidence survives compaction.** Sources and task notes live in `research/<slug>_<date>/` on disk, so long runs never lose citations.
- **Drafts come from notes only.** No invented URLs, numbers, quotes, or DOIs; unsupported claims are cut or marked `[unverified]`.
- **Counter-review is mandatory.** The red team must surface at least three real issues, or it runs again.
- **Mechanical gate.** `scripts/validate_report.py` checks citations, sections, placeholders, and uncited numbers before delivery.
- **Fetched content is data, never instructions.** Prompt injection inside pages is ignored.

## Modes

| Mode | Use for | Sources | Subagents | Output |
|---|---|---|---|---|
| **Quick** | single fact / sanity check | 3–6 | 0 | answer + 3 cites |
| **Standard** (default) | most questions | 12+ | 3–5 | 1.5–4k words |
| **Deep** | decisions, comparisons, contested topics | 20+ | 5–8 | 4–8k words |
| **UltraDeep** | due diligence, literature reviews | 30+ | 8–12 + red team | 8k+ words |

Lenses combine with any depth: **Recency** (last 30 days + community signal), **Enterprise** (company deep dive + SWOT), **Compare** (X vs Y matrix), **Discovery** (trend scan), **Structured-list** (outline-first, human-in-the-loop), **Academic** (papers, DOIs, strength of evidence).

## Pipeline

| Phase | Output |
|---|---|
| **P0 Setup** | Real date, workdir, citation registry, capability check |
| **P1 Scope** | 3–7 sub-questions, task board with non-overlapping scopes |
| **P2 Retrieve** | Parallel subagents, full-page reads for load-bearing sources, evidence objects |
| **P3 Triangulate** | Source registry, authority and independence gates |
| **P4 Outline** | Topic-first, evidence-mapped sections with gaps and counter-claims |
| **P5 Draft** | Prose-first report, per-section confidence, stated `AS_OF` |
| **P6 Red team** | Five adversarial personas, ≥3 real issues, loop back if critical |
| **P7 Verify** | Validator, spot-checks, URL re-fetch, ≤3 fix cycles |
| **P8 Deliver** | `report.md` on disk, brief chat summary with confidence and caveat |

## Install

```bash
# OpenCode
unzip ultimate-deep-research.skill -d ~/.config/opencode/skills/

# Claude Code
unzip ultimate-deep-research.skill -d ~/.claude/skills/

# Any agent that reads a skills directory
mkdir -p <skills-dir>/ultimate-deep-research
cp -r ultimate-deep-research/* <skills-dir>/ultimate-deep-research/
```

The repo root has the flat `ultimate-deep-research-SKILL.md` for platforms that take a single file; the `.skill` archive is the full skill with references, scripts, templates, and tests.

Requires Python 3 (standard library only) for the registry and validator. Web search and page fetch are required; subagents are optional — the skill degrades to sequential mode.

## Usage

Once installed, ask normally. It triggers on "research", "investigate", "compare", "due diligence", "what's the current state of X", "last 30 days", and similar phrasing.

Typical run:

```bash
# 1. Create the workdir and register sources
python scripts/sources.py init --dir research/topic_2026-10-27 --mode standard --as-of 2026-10-27
python scripts/sources.py add --dir research/topic_2026-10-27 \
  --url https://example.com/report --title "Report" --type official \
  --access public --date 2026-10-20

# 2. Write the report, then validate it
python scripts/validate_report.py research/topic_2026-10-27/report.md \
  --sources research/topic_2026-10-27/sources.json --mode standard
```

`sources.py` commands: `init`, `add`, `drop`, `list`, `stats`, `export` (numbered bibliography text).

## Files

| Path | Contents |
|---|---|
| `ultimate-deep-research-SKILL.md` | Flat skill definition (root copy) |
| `ultimate-deep-research.skill` | Full skill archive (zip) |
| `SKILL.md` | Skill definition with pipeline and non-negotiables |
| `references/methodology.md` | Subagent briefs, query craft, evidence objects |
| `references/source-governance.md` | Accessibility/type labels, authority rubric, gates |
| `references/quality-gates.md` | Thresholds, red-team personas, completeness checklist |
| `references/modes.md` | Recency, Enterprise, Compare, Discovery, Structured-list, Academic |
| `references/environments.md` | Per-platform adaptations and continuation protocol |
| `references/lineage.md` | Upstream skills and what was taken from each |
| `scripts/sources.py` | Disk-backed citation registry |
| `scripts/validate_report.py` | Report validator |
| `templates/report_template.md` | Report skeleton with required sections |
| `templates/task_note.md` | Per-task evidence note |
| `tests/test_skill.py` | 15 unit tests (`python3 -m unittest tests.test_skill`) |

## Verify the install

```bash
python3 -m unittest tests.test_skill -v   # 15 tests, ~20s
python3 scripts/validate_report.py --help
```

## License

No license file is included in this repository. Treat it as all rights reserved until one is added.
