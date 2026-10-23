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
| **betterleaks** | OSS | ✅ SAP approved | Aktiver Nachfolger von gitleaks; `.gitleaks.toml`-kompatibel; native JUnit/SARIF; MIT |
| **gitleaks** | OSS | ✅ SAP approved | Feature-complete, Maintenance-Mode; Security-Patches bis auf weiteres |
| **trufflehog** | OSS | ❌ Not in MySoftwareHub | Ruled out |
| **GitHub Secret Scanning** | Platform | ✅ Already active | Native GitHub feature — **already running on the repos** |

## Scoring Criteria — preliminary findings (2026-10-21)

> Sources: Context7 + repo Target Architecture & Quality Standards.
> **SAP MySoftwareHub status (2026-10-23):** betterleaks **approved** ✅ — gitleaks **approved** ✅ — trufflehog **Not Found** ❌
> **GitHub Secret Scanning:** already active on the repos ✅ (platform-native, no install required)
> **gitleaks status:** feature-complete, Maintenance-Mode (Security-Patches only); maintainer shifting focus to betterleaks.

| Criterion | betterleaks | gitleaks | GitHub Secret Scanning |
|-----------|-------------|----------|----------------------|
| **Development status** | ✅ Actively developed (v1.8.1 Aug 2026) | ⚠️ Maintenance-only (v8.30.1 Mar 2026) — security patches only | Platform-managed by GitHub |
| **Detection coverage** | Regex + entropy + keyword; `.gitleaks.toml`-kompatibel; zusätzlich Secret-Validierung & Analyse | Regex + entropy + keyword; extensible via `.gitleaks.toml` | Curated patterns for known secret types; scans entire git history on push |
| **False-positive rate** | Moderate + optionale Secret-Validierung (bessere FP-Reduktion) | Moderate; tunable via allowlists | Low for supported patterns; limited to known provider patterns |
| **GARM / e2e-governance fit** | **Strong** — JUnit-native; feeds `e2e-gate-evaluator`; offline | **Strong** — JUnit-native; feeds `e2e-gate-evaluator`; offline | **Weak** — kein JUnit, kein Pipeline-Exit-Code, kann keinen G1-Gate blockieren |
| **CI / reporting (incl. JUnit XML)** | Native: `json`, `csv`, `junit`, `sarif` | Native: `json`, `csv`, `junit`, `sarif` | GitHub Alerts UI only — not pluggable into `e2e-gate-evaluator` |
| **Migration** | Drop-in: `.gitleaks.toml` und CLI-Flags kompatibel | — | — |
| **Licensing / SAP approval** | **MIT** ✅ — **MySoftwareHub: approved** ✅ | **MIT** ✅ — **MySoftwareHub: approved** ✅ | Platform feature — kein separates Approval nötig |

##### Updated picture (2026-10-23)

**Betterleaks ist der bevorzugte Kandidat für den blocking G1-Gate.**

- Aktiv entwickelt, MySoftwareHub-approved, MIT, JUnit-nativ, `.gitleaks.toml`-kompatibel (Migration von gitleaks = kein Rewrite)
- gitleaks bleibt valide Fallback-Option falls betterleaks im Enterprise-Track-Record-Check nicht besteht
- GitHub Secret Scanning: post-push platform-side backstop — komplementäre Schicht, kein Ersatz für den Pipeline-Gate

**Defense-in-Depth:**
- betterleaks: in-pipeline, pre-merge, blocking, JUnit-native → feeds `e2e-gate-evaluator`
- GitHub Secret Scanning: post-push, platform-side, advisory → fängt was trotzdem durchkommt

## Decisions to Make

### D1 — Tool selection
- [ ] Which tool? → **Lean: betterleaks** (aktiv entwickelt, MySoftwareHub-approved, MIT, JUnit-nativ, `.gitleaks.toml`-kompatibel). gitleaks als Fallback falls betterleaks Enterprise-Track-Record-Check nicht besteht. Rationale muss dokumentiert werden.

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
- [x] ~~Which **SAP-internal scanner** is the approved candidate?~~ → moot; betterleaks approved und ausgewählt.
- [ ] **Add `e2e-secret-scan` row** to the Target Architecture change summary table (currently missing).
- [x] ~~Confirm **trufflehog licensing** and SAP CI egress~~ → resolved: not in MySoftwareHub → ruled out.
- [ ] The referenced `Ansible-Custom-Rules-Implementation.md §3.6` is **not yet in the repo** — track where e2e-secret-scan gets specified.
- [ ] Alignment session with Mario (per Diana's note on E2ETESTAUTO-33, 2026-10-15) — relevant hier?
- [ ] **betterleaks lokal testen** — CLI-Verhalten, FP-Rate auf dem Ansible-Repo, `.gitleaks.toml`-Kompatibilität verifizieren vor finaler D1-Entscheidung.

## Links & References

- **Parent track:** [E2ETESTAUTO-14](https://jira.tools.sap/browse/E2ETESTAUTO-14) — Tooling Evaluation
- **Downstream:** [E2ETESTAUTO-18](https://jira.tools.sap/browse/E2ETESTAUTO-18) — 2.4 Pros/cons matrix
- **Quality Standards (§1.5 secrets, §2.4 complementary):** `docs/e2e-test-automation/Ansible-Quality_Standards-Conventions.md`
- **Target Architecture (gap, e2e-governance layer, change summary):** `docs/e2e-test-automation/Ansible-Collection-Target-Architecture.md`
- **Gate criteria (secret scanning = blocking):** `docs/e2e-test-automation/define-gate-stages-blocking-criteria.md` (E2ETESTAUTO-33)
- **gitleaks:** https://github.com/gitleaks/gitleaks — reporting formats incl. `junit`, `sarif`; `.gitleaks.toml` allowlists
- **trufflehog:** https://github.com/trufflesecurity/trufflehog — `--results=verified`, `--fail` (exit 183), `sarif`/`json`
- [Jira Board — E2ETESTAUTO](https://jira.tools.sap/secure/RapidBoard.jspa?rapidView=63480&projectKey=E2ETESTAUTO)
