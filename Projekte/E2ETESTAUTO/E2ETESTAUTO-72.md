# E2ETESTAUTO-72 — Implement the Framework's Own CI

## Jira Metadata

| Field | Value |
|-------|-------|
| Key | [E2ETESTAUTO-72](https://jira.tools.sap/browse/E2ETESTAUTO-72) |
| Summary | Implement the framework's own CI for relevant code checks, unit/contract tests and package validation |
| Type | Backlog Item |
| Status | Open |
| Priority | Medium |
| Assignee | Maik Smuda |
| Reporter | Mario Dietner (C5422044) |
| Created | 2026-11-04 |
| Due | **2026-11-11** |
| Epic | [E2ETESTAUTO-74](https://jira.tools.sap/browse/E2ETESTAUTO-74) — Establish the code repository and developer feedback loop |

---

## Context

### Projekt-Setup

| Item | Value |
|------|-------|
| CI/CD | GitLab CI (GitHub→GitLab-Sync bringt das Repo ins GitLab) |
| Framework-Repo | [ecs-e2e-testing/e2e-testing-pipeline](https://github.tools.sap/ecs-e2e-testing/e2e-testing-pipeline) |
| Architektur-Repo | [ecs-e2e-testing/e2e-testing-architecture](https://github.tools.sap/ecs-e2e-testing/e2e-testing-architecture) |
| Phase | Phase 2 — Implementation |

### Ziel

Das `e2e-testing-pipeline`-Repository (die E2E Governance-Schicht) bekommt eine **eigene CI-Pipeline**, die seinen Inhalt bei jedem Merge/PR validiert — unabhängig von den Consumer-Collections, die das Framework nutzen.

> ⚠️ Hinweis: Die in der Jira-Beschreibung referenzierte Datei `E2ETESTAUTO-31-pipeline-repository-structure.md` existiert im Architektur-Repo nicht. Die tatsächliche Quelle ist [`E2ETESTAUTO-31-blueprint-design.md`](https://github.tools.sap/ecs-e2e-testing/e2e-testing-architecture/blob/main/docs/E2ETESTAUTO-31-blueprint-design.md) (§2 und §3).

---

## Ist-Stand im Framework-Repo

### Main Branch
Nahezu leer — nur ein einzeiliges `README.md`. Kein CI vorhanden.

### PR #1 — E2ETESTAUTO-71 (noch nicht gemergt)
Branch: [`E2ETESTAUTO-71-repo-layout`](https://github.tools.sap/I772465/e2e-testing-pipeline/tree/E2ETESTAUTO-71-repo-layout) (Autor: Adrian Radu / I772465)
PR: [ecs-e2e-testing/e2e-testing-pipeline/pull/1](https://github.tools.sap/ecs-e2e-testing/e2e-testing-pipeline/pull/1)

Scaffoldet das vollständige Directory-Layout:

```
pipeline/
  e2e-collection-pipeline.yml     ← echter GitLab-CI-Einstiegspunkt für Collections
extensions/
  garm-overrides.yml              ← echtes YAML (GARM Job-Overrides)
  security-overrides.yml          ← echtes YAML (Security-Overrides)
config/
  .ansible-lint                   ← echte Lint-Konfiguration (profile: production, strict: true)
  policy.yml                      ← echte Gate-Policy (G0–G5 mit blocking: true)
atoms/
  linting/
    e2e-lint.yml                  ← Placeholder (echo only)
  testing/
    ansible-test-sanity.yml       ← Placeholder (echo only)
    artifact-test.yml             ← Placeholder (echo only)
  evaluate/
    gate-evaluator.yml            ← Placeholder (echo only, muss when: always behalten)
```

**CI für das Framework-Repo selbst: nicht vorhanden.**

---

## Was zu implementieren ist

Da das Repo via GitHub→GitLab-Sync läuft, wird eine **`.gitlab-ci.yml` im Root** des Framework-Repos benötigt (kein GitHub Actions).

### 1. Code Checks
- **`yamllint`** auf alle `.yml`-Dateien in `atoms/`, `extensions/`, `pipeline/`, `config/`
- **GitLab CI Syntax-Validation** für `pipeline/e2e-collection-pipeline.yml` und alle Atom-Files (z.B. via `gitlab ci lint` API oder Schema-Validator)

### 2. Contract Tests
Strukturelle Konsistenz-Prüfungen (Python-Script oder Shell):

| Check | Beschreibung |
|-------|-------------|
| Policy↔Atoms-Konsistenz | Alle Job-Namen in `config/policy.yml` müssen als Job-Definitionen in den Atoms existieren |
| `when: always` | `gate-evaluator.yml` muss `when: always` haben (Architekturpflicht) |
| `allow_failure: false` | Alle Atoms müssen `allow_failure: false` deklarieren |
| Pfad-Existenz | Alle `local:` Pfade in `e2e-collection-pipeline.yml` müssen auf existierende Dateien zeigen |

### 3. Package Validation
- Vollständigkeitsprüfung: Alle Gate-Stages (G0/G1a/G1b/G1c/G2/G4/G5) im Entry-Point vertreten?
- Wenn Python Gate Evaluator implementiert: Pytest-Run für Unit-Tests

---

## Abhängigkeiten

```
E2ETESTAUTO-71 (PR #1 — Repo-Layout)
    ↓  muss erst gemergt sein (oder als Base-Branch dienen)
E2ETESTAUTO-72 (diese Aufgabe — Framework-eigene CI)
```

**Optionen:**
1. Auf Merge von PR #1 warten → dann gegen `main` entwickeln
2. Von `E2ETESTAUTO-71-repo-layout` ableiten → CI parallel entwickeln

---

## Links & Referenzen

| Ressource | Link |
|-----------|------|
| Jira Ticket | [E2ETESTAUTO-72](https://jira.tools.sap/browse/E2ETESTAUTO-72) |
| Epic | [E2ETESTAUTO-74](https://jira.tools.sap/browse/E2ETESTAUTO-74) |
| PR #1 (Repo-Layout) | [pull/1](https://github.tools.sap/ecs-e2e-testing/e2e-testing-pipeline/pull/1) |
| Framework-Repo | [e2e-testing-pipeline](https://github.tools.sap/ecs-e2e-testing/e2e-testing-pipeline) |
| Blueprint Design | [E2ETESTAUTO-31-blueprint-design.md](https://github.tools.sap/ecs-e2e-testing/e2e-testing-architecture/blob/main/docs/E2ETESTAUTO-31-blueprint-design.md) |
| GARM/SNI Integration | [E2ETESTAUTO-43-garm-sni-pipeline-integration.md](https://github.tools.sap/ecs-e2e-testing/e2e-testing-architecture/blob/main/docs/E2ETESTAUTO-43-garm-sni-pipeline-integration.md) |
| Promotion Rules | [E2ETESTAUTO-37-promotion-rules.md](https://github.tools.sap/ecs-e2e-testing/e2e-testing-architecture/blob/main/docs/E2ETESTAUTO-37-promotion-rules.md) |
| Technical Delivery Plan | [technical-delivery-plan.md](https://github.tools.sap/ecs-e2e-testing/e2e-testing-architecture/blob/main/docs/technical-delivery-plan.md) |
