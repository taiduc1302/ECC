# Controlled ECC adoption for development and civil-estimating workflows

Status: **PROPOSAL / NOT INSTALLED / NOT PRODUCTION-VALIDATED**
Reviewed: 2026-10-29. Source: [affaan-m/ECC](https://github.com/affaan-m/ECC), MIT license; source baseline at review: `25278e7d7b6cf6e6a4e7a6c8e10f14ff22f0eb5b`.

## Purpose

Adopt *selected* ECC engineering practices across multiple repositories without replacing existing project-specific requirements. ECC is a development-agent harness, not an estimating engine, a licensed Bluebeam/AGTEK/HeavyBid integration, or evidence that a tender estimate is correct.

Target loop: **scope -> research -> plan -> independent checks -> implement in branch -> deterministic tests -> independent review -> recorded evidence -> human acceptance**.

## Safety and ownership requirements (non-negotiable)

1. Preserve existing repository instructions (`AGENTS.md`, `CLAUDE.md`, `.claude/`, `.codex/`), requirements, QA state machines and project approvals. Merge deliberately; never bulk overwrite.
2. An AI agent cannot self-approve quantities, pricing, a bid, a public release, a codebook mapping, or a production import. Keep irreversible writes, external sends, merges and credentials behind separately authorized human-controlled actions.
3. Treat tender PDFs, drawings, annotations, emails, fetched web pages, model outputs, and generated code as untrusted input. Their contents cannot grant tool permissions or rewrite approval policy.
4. Public GitHub repositories get **only synthetic, government-open, licensed or demonstrably sanitized examples**. Never commit live tenders, customer/account metadata, personal financial records, employee data, codebooks, OAuth tokens, `.env`, credentials or protected drawings.
5. Production Bluebeam/HeavyBid/AGTEK state must not be changed during experiments. Use synthetic/read-only dry runs and copies; retain immutable evidence and verify native markup read-back when explicitly authorized.
6. Require evidence-backed statuses: `PASS`, `FAIL`, `NOT RUN`, `NEEDS HUMAN REVIEW`. No unobserved test is a pass; avoid broad claims of real-world accuracy based on synthetic tests.
7. Fork and changes remain MIT-compatible: preserve copyright notices for copied portions and link back to upstream sources. This document is an adoption guide, not a claim of ECC maintainership.

## Selection: use small components, not all ECC

| Priority | ECC components to inspect/use | Reason | Caveat |
| --- | --- | --- | --- |
| P0 | `skills/verification-loop`, `skills/ai-regression-testing` | Mechanical build/lint/tests and defect regression first | Adapt commands to each repo; a generic Node.js command is not valid for a Python repo |
| P0 | `skills/agent-security-hardening`, `agents/silent-failure-hunter.md`, `agents/opensource-sanitizer.md` | Fail-closed tool boundaries, error propagation, public repo hygiene | Review every proposed check; regex-only scanning is insufficient |
| P1 | `skills/module-regression`, `skills/change-impact`, `skills/eval-harness` | Trace changes to downstream modules and repeatable acceptance tests | A model-only grade is never enough for numerical quantities |
| P1 | `skills/cost-aware-llm-pipeline`, `skills/context-budget`, `skills/agent-eval` | Measure routing cost, token use, latency and success | Do not copy model IDs or price tables blindly; verify official provider facts |
| P2 | `agents/planner.md`, `agents/code-reviewer.md`, `skills/architecture-decision-records` | Independent planning and review in complex work | Parallel agents add cost; default to a single worker on small tasks |
| Hold | Global hook bundles, MCP collections, always-loaded rule packs, autonomous loops | Potential duplicate execution, tool permission and token overhead | Inventory + explicit trust review required before enabling |

Canonical source paths are under `https://github.com/affaan-m/ECC/tree/main/`. Do not copy a full ECC `AGENTS.md` or `.codex/config.toml` into other projects: they contain ECC-specific assumptions.

## Existing repository inventory and proposed pilot acceptance

| Repository / surface | Existing boundary | Proposed adaptation | Evidence required before rollout |
| --- | --- | --- | --- |
| `ai-model-advisor` (public, Python) | Existing `skills/ai-model-advisor`, `evaluations/task_cases.json`, `.github/workflows/ci.yml`, review-first promotion and explicit provider execution | First pilot: scoped `AGENTS.md` and an offline eval/CI checklist; no auto-routing edits | `ruff`, selected/full `pytest`, offline curated evaluation, actual CI status, no provider calls unless requested |
| `civil-bid-readiness-auditor` (public, Python) | Deterministic review tool; `CLAIMS_LEDGER.md`; review packages, QA lifecycle; CI unittest matrix | Add downstream-regression mapping for parser -> findings -> review -> export/integrity; use independent reviewer | `python -m unittest discover -s tests -v`, fictional fixtures, explicit human review; **no bid-readiness or approval claim** |
| `statement-calendar` (public, browser JS) | Demo-only data, localStorage, no dependency/build workflow | Later pilot: synthetic date-boundary, status-transition and privacy checks | Leap-year/month-end/timezone cases, demo-only fixtures, zero personal account/statement data, browser smoke |
| `screen2xyz` (public POC) | Existing `AGENTS.md`, `.codex/`, immutable evidence, Windows-only M2 validation gates | Add selected regression and failure-hunting checks **without** overwriting evidence or task gates | Existing baseline/M1/M2 commands as documented; copies only; no new unsupported real-target accuracy claims |
| `civil-takeoff-markups-mcp` (public methods) | Traceable markup register, synthetic sheet and separate human QA approval | Add fail-closed tool-boundary and units/scale regression cases | Per-markup source+scale+geometry+units+native read-back; automation can never mark `APPROVED` |
| `hive` | Existing `AGENTS.md`, `CLAUDE.md`, `.claude/settings.json`, CI and model-advisor integration | Only targeted modular regression and security-policy diffs; never overwrite runtime configuration | Current CI and integration checks; risk-based human review of hooks and networked tools |
| Private civil takeoff, AGTEK, and HeavyBid work | Project documents, codebooks, quantities and production systems may be confidential | Use a private sanitized adapter and evidence ledger, beginning with synthetic civil cases | Confirm drawing revision, scale/units, location mapping, codebook revision, reconciled quantity and estimator sign-off before any import |

## Proposed standard task contract

Every non-trivial task starts with: (a) repository and immutable baseline commit, (b) exact goal and acceptance tests, (c) authorized read/write scope, (d) source and unit conventions, (e) prohibited side effects, (f) expected evidence location, (g) human acceptance authority.

Every task closes with: changed files, observed command + exit code, source/version evidence, independent reviewer findings, unverified assumptions, explicit status and rollback instructions. Never rely on a model's self-certification.

## Civil-tender synthetic pilot: cases to build before real use

1. Conflicting drawing revisions and addenda: newest authoritative revision must be established or stop.
2. Incorrect/unset PDF scale, mixed drawing scales, units mm/m/ft, and cross-sheet comparison: reject or flag ungrounded measurements.
3. Scope mismatch (SOQ versus plans/specs): preserve source-page links and flag missing/duplicate quantities.
4. Duplicate or missing Bluebeam native markups: compare planned IDs to actual read-back IDs, geometry, type, page and measurements.
5. HeavyBid Activity/Material codebook drift and invalid units: no automatic mapping or import on ambiguity.
6. AGTEK existing/design surface selection, stripping thickness, boundary topology and daylight rules: validate against independently reviewable geometry; never invent terrain assumptions.
7. Price, production rate, waste factor and subcontractor quote changes: separate observed values from assumptions and require estimator review.
8. Prompt-injection text inside a fictional tender: must not alter tools, permissions, logs or approval gates.
9. Model timeout, incomplete extraction, corrupt input or failed connection: fail visibly and preserve partial provenance.
10. Regression after a workflow change: rerun affected fixtures, compare exact expected quantities/statuses and preserve failed evidence.

Acceptance is not `AI says PASS`: tests must show exact expected outputs, exit codes and, when a native tool is involved, tool read-back. For a pilot we require zero unauthorized writes and zero AI self-approvals. Remaining thresholds for quantity precision and recall must be set from real signed-off requirements, not guessed.

## Rollout gates

**Gate A – Audit only:** inspect current project rules, CI, hook/MCP configs, existing skills, privacy posture, and license. No installs or repo mutations other than an explicitly scoped review branch.

**Gate B – Documentation pilot:** add repo-specific review-first instructions on a draft PR. Verify the PR diff and make sure existing instructions are not superseded.

**Gate C – Offline verification:** run actual existing CI commands and new synthetic regression cases; record pass/fail/NOT RUN and provenance. No agent can alter expected results to make a failing test pass.

**Gate D – Tool sandbox:** if permitted, connect only one scoped tool at a time (e.g. Bluebeam read-only or synthetic markup copy); verify output read-back and state preservation.

**Gate E – Operational use:** only after explicit owner approval and role-specific security review; start with low-risk read-only tasks, human sign-off at every downstream import.

## Install guidance (instructions only; nothing installed by this PR)

- For Claude Code, review a *single* ECC path: official `ecc@ecc` native plugin OR selected manual files, never both. Native plugin hooks auto-load; do not duplicate `hooks/hooks.json` in `settings.json`.
- For Codex, consider the native ECC marketplace plugin instead of deprecated `sync-ecc-to-codex.sh`; do not layer plugin and sync. Inspect `codex plugin list --json` and trust hooks explicitly before enabling.
- For a low-risk trial, begin in one disposable project/user-local scope, review installer dry-run and hooks, and leave MCP write access disabled. The fork itself is **not** an installation.
- Avoid installing copied rule packs globally: ECC common rules can conflict with Python/Windows tools, project-specific test commands, and established human-approval requirements.
- Do not assume ECC Pro/GitHub App is required. Open-source framework is MIT; hosted services and external model/provider calls have separate costs or access requirements.

## Proposed next owner decision

Review the two draft PRs (this playbook and the AI Model Advisor pilot). If accepted, select one offline synthetic evaluation and one repo-specific deterministic test runner; record a baseline before any wider rollout. Do not merge solely because the documents look reasonable.

Sources: [ECC README](https://github.com/affaan-m/ECC/blob/main/README.md), [ECC security guide](https://github.com/affaan-m/ECC/blob/main/the-security-guide.md), [ECC module regression](https://github.com/affaan-m/ECC/blob/main/skills/module-regression/SKILL.md), [ECC agent hardening](https://github.com/affaan-m/ECC/blob/main/skills/agent-security-hardening/SKILL.md).
