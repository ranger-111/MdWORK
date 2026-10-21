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
- **E2ETESTAUTO-8** — Quality Standards & Conventions *(§1.4 task-level secret-handling discipline — complementary control)*
- **E2ETESTAUTO-14** — Tooling Evaluation (parent track)

---

## Goal

Select the secret/credential scanning **tool** and its **operating scope** for ECS Ansible content, and confirm it as a genuinely new control (not a Checkov extension).

This is a **greenfield decision** — unlike the linting/reporting evaluations (keep/tune/replace), there is **no existing secret-scanning control** to compare against.

---

## Context

### Why this is a confirmed gap

- **No GARM atom performs secret/credential detection today** — not even on the more mature `sap_ecs.configuration_management` pipeline.
- **Checkov** (the existing static-IaC-analysis atom) was previously *assumed* to cover this. The Target Architecture **corrected that assumption** and reclassified secret scanning as a confirmed gap.
- The gap is to be filled by a new control: the **`e2e-secret-scan` job (G1, blocking)** in the proposed **e2e-governance layer**.

### Candidates named so far

| Candidate | Type | Notes |
|-----------|------|-------|
| **gitleaks** | OSS | Also referenced in E2ETESTAUTO-33 §2/§3 |
| **trufflehog** | OSS | Also referenced in E2ETESTAUTO-33 §2/§3 |
| **SAP-internal approved scanner** | Internal | Name TBD — check SAP-internal approval status |

> None has been evaluated against ECS-specific criteria yet.

---

## Scoring Criteria (from Acceptance Criteria)

To be scored per tool:

| Criterion | gitleaks | trufflehog | SAP-internal | Notes |
|-----------|:--------:|:----------:|:------------:|-------|
| Detection coverage | | | | |
| False-positive rate | | | | |
| GARM / e2e-governance fit | | | | |
| CI / reporting support (incl. JUnit XML) | | | | |
| Maintainability | | | | |
| Licensing / SAP-internal approval status | | | | |

*(fill in during evaluation — scoring scale TBD, align with E2ETESTAUTO-18 matrix format)*

---

## Decisions to Make

### D1 — Tool selection
- [ ] Which tool? → rationale required
- Owner: Maik (eval) → sign-off TBD

### D2 — Scan scope
- [ ] Which **paths** are scanned?
- [ ] Which **branches**?
- [ ] Which **stages**?
- [ ] How are findings **triaged**?

### D3 — Severity / enforcement
- [ ] Blocking (G1) **from day one**, or **warn-first → blocking**?
- Note: E2ETESTAUTO-33 §3 currently proposes secret scanning = **Blocking, no exceptions**. Reconcile the final decision here back into -33.

### D4 — Relationship to Quality Standards §1.4
- [ ] Document as **complementary, not duplicate** controls
  - §1.4 = task-authoring secret-handling discipline (how developers avoid putting secrets in code)
  - This task = pipeline-level detection control (catches what slips through)

---

## Acceptance Criteria Checklist

- [ ] Tool selected with documented rationale, scored against agreed criteria
- [ ] Confirmed as a genuinely new control, not a Checkov extension (cross-ref Target Architecture change summary + gap finding)
- [ ] Scan scope and severity decided (paths/branches/stages; blocking day-1 vs. warn-first)
- [ ] Relationship to Quality Standards §1.4 documented as complementary
- [ ] Evaluation captured against scoring criteria and handed to E2ETESTAUTO-18 (2.4 pros/cons matrix)

---

## Open Questions / Notes

- [ ] Confirm **Mario vs. Martin Dietner** — reporter here is "Mario Dietner"; E2ETESTAUTO-33 sign-off listed "Martin Dietner (standards)". Same person?
- [ ] Which SAP-internal scanner is the approved candidate? (name, approval reference)
- [ ] Does the e2e-governance layer / `e2e-secret-scan` job already have a stub in the Target Architecture doc?
- [ ] Alignment session with Mario (per Diana's note on E2ETESTAUTO-33, 2026-10-15) — relevant here?

---

## Links & References

- **Parent track:** [E2ETESTAUTO-14](https://jira.tools.sap/browse/E2ETESTAUTO-14) — Tooling Evaluation
- **Downstream:** [E2ETESTAUTO-18](https://jira.tools.sap/browse/E2ETESTAUTO-18) — 2.4 Pros/cons matrix
- **Quality Standards §1.4:** `docs/e2e-test-automation/Ansible-Quality_Standards-Conventions.md`
- **Target Architecture (gap reclassification, e2e-governance layer):** `docs/e2e-test-automation/Ansible-Collection-Target-Architecture.md`
- **Gate criteria (secret scanning = blocking):** `docs/e2e-test-automation/define-gate-stages-blocking-criteria.md` (E2ETESTAUTO-33)
- [Jira Board — E2ETESTAUTO](https://jira.tools.sap/secure/RapidBoard.jspa?rapidView=63480&projectKey=E2ETESTAUTO)
