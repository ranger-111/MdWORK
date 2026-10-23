# E2ETESTAUTO-57 — Evaluate Secret/Credential Scanning Tooling

> **Working document** — collect all information here. Decide-and-document task. Draft for later handoff to PR + wiki.

## Jira Metadata

| Field | Value |
|-------|-------|
| Key | [E2ETESTAUTO-57](https://jira.tools.sap/browse/E2ETESTAUTO-57) |
| Summary | Evaluate secret/credential scanning tooling |
| Type | Task |
| Status | In Progress |
| Priority | Medium |
| Assignee | Maik Smuda |
| Reporter | Mario Dietner (external — Service) |
| Created | 2026-10-19 |
| Track | 2. Tooling Evaluation & Selection |

## Related Issues

- **E2ETESTAUTO-18** — 2.4 Pros/cons matrix *(downstream — this evaluation feeds it)*
- **E2ETESTAUTO-33** — 3.1 Gate stages & blocking/non-blocking criteria *(classified secret scanning as blocking G1 — this task picks the tool and finalizes warn-first vs. blocking-day-1)*
- **E2ETESTAUTO-8** — Quality Standards & Conventions *(task-level secret-handling discipline — complementary control)*
- **E2ETESTAUTO-14** — Tooling Evaluation (parent track)

---

## Goal

Select the secret/credential scanning **tool** and its **operating scope** for ECS Ansible content, and confirm it as a genuinely new control (not a Checkov extension).

This is a **greenfield decision** — unlike the linting/reporting evaluations (keep/tune/replace), there is **no existing secret-scanning control** to compare against.

---

## Context

### Why this is a confirmed gap

- **No GARM atom performs secret/credential detection today** — not even on the more mature `sap_ecs.configuration_management` pipeline.
- **Checkov** (the existing `security/checkov.yml` atom) is **static IaC analysis only** — it does not perform secret detection. It was previously *assumed* to cover this; the Target Architecture corrected the assumption.
- **`no-log-password`** (ansible-lint) is a lint-level aid, **not a scanner**.
- The gap is to be filled by a new control: the **`e2e-secret-scan` job (G1, blocking)** in the proposed **e2e-governance layer**.

> **Evidence (from the repo):**
> - Quality Standards & Conventions, security table (line ~580): *"Confirmed gap: no current GARM atom performs secret/credential scanning — Checkov is static IaC analysis only, and `no-log-password` is a lint-level aid, not a scanner. A dedicated secret-scanning job is a new control to be added, tracked in Custom Rules §3.6 (e2e-secret-scan) and the Target Architecture change summary."*
> - Target Architecture, GARM security atoms: Checkov is advisory→blocking; BDBA/SonarQube advisory; **no secret scanner listed**.

### ⚠️ Open finding — e2e-secret-scan not yet in Target Architecture change summary

The `e2e-secret-scan` control is named in Quality Standards and in the ticket, but the **Target Architecture change summary table has no `e2e-secret-scan` row yet** (it lists lint, molecule, sanity, checkov/security, metadata, artifact-test, gate-evaluator — but not secret scanning). Closing this task should include **adding the row** to the Target Architecture change summary + referencing the gap finding (AC #2).

### Candidates named so far

| Candidate | Type | Status | Notes |
|-----------|------|--------|-------|
| **gitleaks** | OSS | ✅ SAP approved | Native JUnit/SARIF output; MIT; offline |
| **trufflehog** | OSS | ❌ Not in MySoftwareHub | Ruled out |
| **GitHub Secret Scanning** | Platform | ✅ Already active | Native GitHub feature — **already running on the repos** |
| **SAP-internal approved scanner** | Internal | ❓ TBD | Name/approval still unclear |

## Scoring Criteria — preliminary findings (2026-10-21)

> Sources: Context7 (gitleaks `/gitleaks/gitleaks`, both current docs) + repo Target Architecture & Quality Standards.
> **SAP MySoftwareHub status (2026-10-23):** gitleaks **approved** ✅ — trufflehog **Not Found / not approved** ❌
> **GitHub Secret Scanning:** already active on the repos ✅ (platform-native, no install required)

| Criterion | gitleaks | GitHub Secret Scanning |
|-----------|----------|----------------------|
| **Detection coverage** | Regex + entropy + keyword rules; extensible via `.gitleaks.toml`. Scans **git history + filesystem**. | Pattern-based; covers GitHub's curated list of known secret types (tokens, keys, certs); scans entire git history on push. |
| **False-positive rate** | Moderate; no live verification → tunable via allowlists. | Low for supported patterns (curated, high-confidence); limited to known provider patterns. |
| **GARM / e2e-governance fit** | **Strong** — native JUnit matches the reporting contract; feeds `e2e-gate-evaluator` directly; offline; runs in GARM pipeline. | **Weak as a GARM gate** — GitHub-platform feature, not a CI job artifact; no JUnit output; alerts via GitHub UI/webhooks, not pipeline exit codes. Cannot directly block a GARM gate stage. |
| **CI / reporting (incl. JUnit XML)** | **Native: `json`, `csv`, `junit`, `sarif`.** Direct fit for JUnit reporting contract (E2ETESTAUTO-17). | No JUnit output; GitHub Alerts UI only. Not pluggable into `e2e-gate-evaluator`. |
| **Complementary / overlap** | Runs in-pipeline (pre-merge gate, G1 blocking). Catches secrets before they reach GitHub. | Runs post-push on GitHub side. Catches secrets that reach the remote. **Complementary layer, not a replacement.** |
| **Licensing / SAP approval** | **MIT** ✅ — **SAP MySoftwareHub: approved** ✅ | GitHub platform feature — no separate approval needed; already in use. |

##### Updated picture (2026-10-23)

GitHub Secret Scanning is already active → it provides a **platform-level backstop** (post-push). That does **not** replace the need for a GARM pipeline gate: GitHub Scanning has no JUnit output, no pipeline exit code, and cannot block a G1 gate stage.

**Decision: gitleaks as the blocking G1 gate + GitHub Secret Scanning as the complementary platform layer.**

- gitleaks: in-pipeline, pre-merge, blocking, JUnit-native → feeds `e2e-gate-evaluator`
- GitHub Secret Scanning: post-push, platform-side, advisory → catches anything that slips through

This is a defense-in-depth pattern, not duplication. Both controls have distinct trigger points and outputs.

## Decisions to Make

### D1 — Tool selection
- [ ] Which tool (or hybrid)? → rationale required
- Preliminary lean: **gitleaks** for the blocking gate (JUnit-native, MIT, offline). Confirm after SAP-internal scanner is assessed.

### D2 — Scan scope
- [ ] Which **paths** are scanned? (whole repo vs. changed files)
- [ ] Which **branches**? (MR/PR validation vs. default)
- [ ] Which **stages**? (G1 security stage in e2e-governance)
- [ ] Git **history** scan vs. working-tree only?
- [ ] How are findings **triaged** / allowlisted? (`.gitleaks.toml` allowlist vs. trufflehog verification)

### D3 — Severity / enforcement
- [ ] Blocking (G1) **from day one**, or **warn-first → blocking**?
- Note: E2ETESTAUTO-33 §3 proposes secret scanning = **Blocking, no exceptions**. Reconcile the final decision here back into -33.

### D4 — Relationship to Quality Standards (task-level secret-handling)
- [ ] Document as **complementary, not duplicate** controls:
  - Task-authoring discipline (dev avoids committing secrets): QS **§1.5 "Input validation and secrets"** (lines ~379–399) — `no_log: true`, no hardcoded secrets, approved secret-management integration.
  - Complementary-control relationship stated in QS **§2.4**.
  - This task = pipeline-level detection control (catches what slips through).

---

## Acceptance Criteria Checklist

- [ ] Tool selected with documented rationale, scored against agreed criteria
- [ ] Confirmed as a genuinely new control, not a Checkov extension (cross-ref Target Architecture change summary + gap finding)
- [ ] Scan scope and severity decided (paths/branches/stages; blocking day-1 vs. warn-first)
- [ ] Relationship to Quality Standards task-level secret rules documented as complementary
- [ ] Evaluation captured against scoring criteria and handed to E2ETESTAUTO-18 (2.4 pros/cons matrix)

---

## Open Questions / Notes

- [ ] **QS section reference mismatch:** the ticket cites "Quality Standards §1.4", but §1.4 is the *naming matrix*. The secret-handling discipline is in **§1.5**, and the pipeline-complementary relationship is **§2.4**. Confirm the intended reference before writing it up.
- [ ] **Confirm Mario vs. Martin Dietner** — reporter here is "Mario Dietner"; E2ETESTAUTO-33 sign-off listed "Martin Dietner (standards)". Same person?
- [x] ~~Which **SAP-internal scanner** is the approved candidate?~~ → **moot for D1**: gitleaks is approved and selected; SAP-internal column no longer blocks the decision.
- [ ] **Add `e2e-secret-scan` row** to the Target Architecture change summary table (currently missing).
- [x] ~~Confirm **trufflehog licensing** and SAP CI egress~~ → **resolved (2026-10-23):** trufflehog not found in SAP MySoftwareHub → ruled out entirely.
- [ ] The referenced `Ansible-Custom-Rules-Implementation.md §3.6` is **not yet in the repo** — track where e2e-secret-scan gets specified.
- [ ] Alignment session with Mario (per Diana's note on E2ETESTAUTO-33, 2026-10-15) — relevant here?

## Links & References

- **Parent track:** [E2ETESTAUTO-14](https://jira.tools.sap/browse/E2ETESTAUTO-14) — Tooling Evaluation
- **Downstream:** [E2ETESTAUTO-18](https://jira.tools.sap/browse/E2ETESTAUTO-18) — 2.4 Pros/cons matrix
- **Quality Standards (§1.5 secrets, §2.4 complementary):** `docs/e2e-test-automation/Ansible-Quality_Standards-Conventions.md`
- **Target Architecture (gap, e2e-governance layer, change summary):** `docs/e2e-test-automation/Ansible-Collection-Target-Architecture.md`
- **Gate criteria (secret scanning = blocking):** `docs/e2e-test-automation/define-gate-stages-blocking-criteria.md` (E2ETESTAUTO-33)
- **gitleaks:** https://github.com/gitleaks/gitleaks — reporting formats incl. `junit`, `sarif`; `.gitleaks.toml` allowlists
- **trufflehog:** https://github.com/trufflesecurity/trufflehog — `--results=verified`, `--fail` (exit 183), `sarif`/`json`
- [Jira Board — E2ETESTAUTO](https://jira.tools.sap/secure/RapidBoard.jspa?rapidView=63480&projectKey=E2ETESTAUTO)
