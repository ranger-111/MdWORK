# E2ETESTAUTO-47: Define Molecule Convergence and Idempotency Test Scenarios for Pilot

**Status:** In Progress  
**Due:** 2026-10-21 (überfällig)  
**Assignee:** Maik Smuda  
**Reporter:** Diana Simina  
**Parent:** E2ETESTAUTO-39 — US5: Define Functional Test Strategy  
**Jira:** https://jira.tools.sap/browse/E2ETESTAUTO-47  

---

## Ziel

Definiere die konkreten Molecule-Testszenarien für die Pilot-Collection `sap_ecs.configuration_management`:
1. **Convergence-Tests** — Was wird geprüft, wenn ein Role zum ersten Mal angewendet wird?
2. **Idempotency-Tests** — Was wird geprüft, wenn ein Role ein zweites Mal angewendet wird (keine Änderungen erwartet)?

Das Ergebnis ist eine strukturierte Szenarienliste + Mapping auf die Blocking-Criteria aus E2ETESTAUTO-33.

---

## Kontext & Abhängigkeiten

### Upstream
| Ticket | Status | Was es liefert |
|--------|--------|----------------|
| E2ETESTAUTO-8 — Quality Standards | Delivered | Coding/Lint-Standards, Behavior Profiles, Idempotency-Regeln (§3.1) |
| E2ETESTAUTO-33 — Blocking Criteria | In Progress / PR #30 | Molecule pass/fail = Blocking, Idempotency = Blocking |
| E2ETESTAUTO-5 — Molecule Coverage Audit | Delivered | Rollenliste + Coverage-Status (13/33 Rollen = 39%) |
| E2ETESTAUTO-14 — Tooling Evaluation | Closed | Molecule (Docker driver) als Unit-Test-Tool bestätigt |

### Sibling Tasks (US5)
- E2ETESTAUTO-48 — Define negative test scenarios
- E2ETESTAUTO-49 — Define multi-platform matrix (RHEL + SLES)

### Downstream
- E2ETESTAUTO-50 — 6.1 Define inventory data requirements → braucht die Szenarien aus diesem Ticket
- E2ETESTAUTO-45 — Document blocking/non-blocking per stage in pipeline architecture

---

## Pilot-Collection: sap_ecs.configuration_management

Referenzimplementierung für das gesamte E2E Test Automation Programm.

**Aktueller Stand (aus E2ETESTAUTO-5 Coverage Audit, 2026-07-13):**
- Gesamtrollen: 33
- Molecule-Coverage: 13/33 Rollen = **39%**
- Alle 13 Szenarien nutzen Docker-Driver
- Idempotency-Checks implementiert: **0** (kritische Lücke)
- SLES- und RHEL-Matrix: aktiv wo Szenarien vorhanden
- Behavior Profile (aus QS §3.1): **Desired-state configuration**

### Rollen mit Molecule-Szenarien (13/33)
| Szenario | Has verify.yml | Idempotency |
|----------|:-:|:-:|
| certificates | ❌ | ❌ |
| cgs_nat | ❌ | ❌ |
| cis_sftp | ✅ | ❌ |
| cis_smb | ✅ | ❌ |
| cis_smb_podman_adjoin | ✅ | ❌ |
| cms | ✅ | ❌ |
| default | ❌ | ❌ |
| firewall | ✅ | ❌ |
| maintain_etc_hosts | ✅ | ❌ |
| os_base | ✅ | ❌ |
| os_extend | ✅ | ❌ |
| os_hardening | ✅ | ❌ |
| vars_map | ✅ | ❌ |

### Pilot-Kandidaten für Idempotency-Rollout
| Prio | Rolle | Begründung |
|:----:|-------|-----------|
| P1 | `os_base` | Referenzimplementierung; reichstes verify.yml |
| P2 | `maintain_etc_hosts` | Einfach, isoliert; gut für SC-05 (preserved config) |
| P3 | `firewall` | Deckt SC-05 ab (existierende Regeln erhalten) |
| P4 | `os_hardening` | Hoher Wert; keine bekannten Ausnahmen |
| P5 | `manage_sshd` *(kein Szenario)* | Einfachste neue Szenario-Kandidatin (60 Zeilen) |

---

## Molecule-Teststruktur (Referenz)

Der Molecule-Lifecycle laut QS §3.1 (GSATA-TESTING-#101):

```
molecule test -s <scenario>
  → create
  → prepare
  → converge    ← converge.yml
  → idempotence ← converge zweimal, alle Tasks müssen beim 2. Run "ok" melden
  → verify      ← verify.yml (independent state assertions)
  → destroy
```

**Pflichtstruktur pro Szenario:**
```
molecule/<scenario>/
  molecule.yml     # driver: docker, platform images (erbt GARM-Core-Defaults)
  converge.yml     # play: apply the role
  verify.yml       # play: assert final state (PFLICHT)
```

**Docker-Image (aktuell in sap_ecs.configuration_management):**  
`gmpcisautomation.int.repositories.cloud.sap/cis-automation-container/<distro>:base`  
*(Offen: Migration auf GARM-Standard-Images klären mit Mario)*

---

## Szenario-Kategorien (Behavior Profile: Desired-State Configuration)

| # | Szenario-Familie | Convergence-Check | Idempotency-Check | Prio |
|---|-----------------|-------------------|-------------------|------|
| SC-01 | **Absent/incorrect state** | Role bringt Zielzustand | 2. Run: alle Tasks `ok` | Blocking |
| SC-02 | **Desired state already present** | Keine ungewollten Änderungen | 2. Run: alle Tasks `ok` | Blocking |
| SC-03 | **Invalid input** | Fehler korrekt gemeldet, keine Änderung | N/A | Blocking |
| SC-04 | **Prerequisite failure** | Korrekte Fehlerbehandlung | N/A | Blocking |
| SC-05 | **Preserved unrelated config** | Unrelated state unverändert | 2. Run: kein Drift | High |
| SC-06 | **Service verification** | Service-Status in verify.yml bestätigt | Service bleibt up | High |

---

## Idempotency-Regeln (GSATA-IDEMPOTENCY-#100 + EAAI-ANS-#108)

1. **1. Convergence** → Tasks dürfen `changed` melden
2. **2. Convergence** → alle Tasks müssen `ok` melden (kein `changed`)
3. Falls Task beim 2. Run `changed` → **Idempotency-Test FAILED** (Blocking)
4. `verify.yml` prüft Post-Conditions nach beiden Runs unabhängig

**Ausnahmen (müssen dokumentiert sein):**
- Operational Actions (Agent-Restart, Passwort-Reset) → eigenes Behavior Profile
- Explizit nicht-idempotente Operationen → Dokumentation + Sign-off Martin Dietner erforderlich

---

## Offene Fragen

| # | Frage | Owner |
|---|-------|-------|
| 1 | Docker-Registry für neue Szenarien: CIS-Images oder GARM-Standard? | Mario / Sebastian Stoschek |
| 2 | `MOLECULE_CI_CONTINUE_ON_ERROR` nach Enforcement: deprecated, restricted oder audited? | Sebastian Stoschek |
| 3 | Passt `manage_sshd` als neue Szenario-Kandidatin in den Productization-Pilot-Scope? | Mario / Red Hat |
| 4 | Gibt es Rollen mit intentionally non-idempotent operations (Ausnahmen nötig)? | Mario |

---

## Acceptance Criteria

- [ ] Behavior Profile für Pilot-Rollen zugewiesen (Desired-state)
- [ ] Szenario-Familien SC-01–SC-06 mit Convergence + Idempotency-Mapping definiert
- [ ] Idempotency-Contract dokumentiert (Pass/Fail-Regeln, Enforcement via GARM)
- [ ] Coverage-Status aller 13 vorhandenen Szenarien dokumentiert
- [ ] P1–P5 Rollout-Kandidaten mit Begründung festgelegt
- [ ] Docker-Registry-Frage geklärt (Offene Frage #1)
- [ ] Dokument reviewed von Sebastian Stoschek / Martin Dietner

---

## Links

- [Jira E2ETESTAUTO-47](https://jira.tools.sap/browse/E2ETESTAUTO-47)
- [E2ETESTAUTO-33 — Blocking Criteria PR #30](https://github.tools.sap/I775566/fuzzy-octo-invention/pull/30)
- [E2ETESTAUTO-5 — Molecule Coverage Audit](https://jira.tools.sap/browse/E2ETESTAUTO-5)
- [Molecule Docs](https://docs.ansible.com/projects/molecule/)
