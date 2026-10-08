# E2ETESTAUTO-33 — Define Gate Stages and Blocking/Non-Blocking Criteria

## Jira Metadata

| Field | Value |
|-------|-------|
| Key | [E2ETESTAUTO-33](https://jira.tools.sap/browse/E2ETESTAUTO-33) |
| Summary | 3.1- Define gate stages and blocking/non-blocking criteria per stage |
| Type | Task |
| Status | In Progress |
| Priority | Medium |
| Assignee | Maik Smuda |
| Reporter | Diana Simina |
| Created | 2026-09-10 |
| Due | **2026-10-21** |
| Last Updated | 2026-10-08 (moved to In Progress by Diana Simina) |

## Related Issues

- **E2ETESTAUTO-32** — US32: Define Quality Gate Model *(Parent story, due 21 Oct)*
- **E2ETESTAUTO-34** — 3.2 — Define exception and grandfathering process *(sibling)*
- **E2ETESTAUTO-36** — 3.3 — Define false positive handling rules *(sibling)*
- **E2ETESTAUTO-37** — 3.4 — Define promotion rules (what passes a gate) *(sibling)*
- **E2ETESTAUTO-45** — 4.4 — Document blocking vs. non-blocking behavior per stage *(overlaps — coordinate)*

---

## Context

### Project Setup

| Item | Value |
|------|-------|
| CI/CD | GitLab CI (GARM-based pipeline with atoms) |
| Primary repo | `sap_ecs.configuration_management` (reference implementation) |
| Phase | Phase 2 — Blueprint and Design |
| Epic | [E2ETESTAUTO-31](https://jira.tools.sap/browse/E2ETESTAUTO-31) — Phase 2 (due 31 Oct 2026) |

### Dependencies

This task **depends on** US8 and US14 being closed by 14 Oct:
- US8 (E2ETESTAUTO-8) — Quality Standards & Conventions → defines *what* to gate on
- US14 (E2ETESTAUTO-14) — Tooling Evaluation → defines *which tools* produce gate signals

US32 (this task's parent) **feeds into**:
- US38 — Pipeline Architecture (E2ETESTAUTO-42/43/44/45)
- US41 — Formal Sign-off (due 28 Oct)

---

## Goal

Define:
1. **Which pipeline stages exist** and what each validates
2. **For each stage:** does a failure block the merge or only warn?
3. **Enforcement model:** immediate / gradual / per-collection opt-in

---

## Existing Pipeline Stages (from technical-architecture.md)

| # | Stage | Tools | Current behavior |
|---|-------|-------|-----------------|
| 1 | **Static Analysis** | ansible-lint, yamllint, codespell, shellcheck, pylint/ruff | Exists in GARM pipeline |
| 2 | **Security** | Secret scanning (gitleaks/trufflehog), input validation (argument_specs) | Secret scanning not yet configured |
| 3 | **Unit Tests** | Molecule (Docker driver), idempotency check, multi-platform matrix | Exists — SLES + RHEL matrix |
| 4 | **Integration Tests** | SPC/TIC trigger, TIC/Plato validation, negative scenarios | `integration_test` stage is a placeholder — no jobs yet |
| 5 | **Quality Gate** | Test report (coverage %, pass/fail) → merge allow/block | Not yet enforced |

---

## Blocking vs. Non-Blocking — Decision Framework

### Open Architecture Decision (from technical-delivery-plan.md)

The enforcement model is explicitly listed as an **open decision**:

| Option | Description | Risk |
|--------|-------------|------|
| **Immediate** | All failures block merge from day one | High disruption if existing code has violations |
| **Gradual** | Warnings → errors over N weeks | Lower disruption, requires governance to enforce transition |
| **Per-collection opt-in** | Each repo enables blocking independently | Slowest rollout, inconsistent standards |

### Proposed Criteria per Stage

| Stage | Proposed behavior | Rationale |
|-------|------------------|-----------|
| Static Analysis | **Blocking** | Lint failures are fast, low-noise, and directly reflect standards compliance |
| Security (secret scanning) | **Blocking** | Any secret in code is a hard stop — no exceptions |
| Security (input validation) | **Warning initially → Blocking** | Gradual: existing roles may lack `argument_specs`; 4-week ramp |
| Unit Tests (Molecule) | **Blocking** | Core validation — a failing Molecule test = broken role |
| Unit Tests (idempotency) | **Blocking** | Non-idempotent roles are a critical defect |
| Integration Tests | **Non-blocking (Phase 2)** | Stage is a placeholder — blocking requires full SPC/TIC automation first |
| Quality Gate report | **Blocking** | Gate exists only if report passes |

> ⚠️ These are proposals — need sign-off from Martin Dietner (standards), Sebastian Stoschek (architecture), and Productization (E2ETESTAUTO-13).

---

## Open Questions

- [ ] Enforcement model decision: immediate vs gradual vs per-collection? → Owner: Sebastian + Martin
- [ ] What severity levels from ansible-lint are blocking? (error only, or also warning?)
- [ ] How are `MOLECULE_CI_CONTINUE_ON_ERROR` and `SLES_MOLECULE`/`RHEL_MOLECULE` vars factored in — do they override blocking?
- [ ] Grandfathering: does existing code that currently fails get exempted? → E2ETESTAUTO-34
- [ ] False positive process: who can override a blocked gate? → E2ETESTAUTO-36
- [ ] Coordinate overlap with E2ETESTAUTO-45 (4.4 — document blocking/non-blocking per stage in pipeline architecture)

---

## Links & References

- [Jira Board — E2ETESTAUTO](https://jira.tools.sap/secure/RapidBoard.jspa?rapidView=63480&projectKey=E2ETESTAUTO)
- [Technical Architecture](../fuzzy-octo-invention/docs/e2e-test-automation/technical-architecture.md)
- [Technical Delivery Plan](../fuzzy-octo-invention/docs/e2e-test-automation/technical-delivery-plan.md)
- [Current CI/CD Pipeline Configuration](../fuzzy-octo-invention/docs/e2e-test-automation/document-current-cicd-pipeline-configuration.md)
- **Parent:** [E2ETESTAUTO-32](https://jira.tools.sap/browse/E2ETESTAUTO-32) — US32: Define Quality Gate Model
