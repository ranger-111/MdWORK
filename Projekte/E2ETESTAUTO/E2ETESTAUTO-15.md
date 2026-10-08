# E2ETESTAUTO-15 — Evaluate Molecule Drivers

## Jira Metadata

| Field | Value |
|-------|-------|
| Key | [E2ETESTAUTO-15](https://jira.tools.sap/browse/E2ETESTAUTO-15) |
| Summary | 2.1- Evaluate Molecule drivers (Docker, Podman, delegated, cloud-based) |
| Type | Task |
| Status | In Progress |
| Priority | Medium |
| Assignee | Maik Smuda |
| Reporter | Diana Simina |
| Created | 2026-07-10 |
| Last Updated | 2026-09-29 |
| ETA | **2026-10-09** (per Diana Simina comment) |

## Status History

| Status | Duration |
|--------|----------|
| Open | 81d 4h 53m (2026-07-10 → 2026-09-29) |
| In Progress | since 2026-09-29 (moved by Diana Simina) |

## Related Issues

- **E2ETESTAUTO-55** — 2.5- SPIKE: Molecule driver selection — Docker vs. Podman vs. delegated *(Open, same assignee)*
- **E2ETESTAUTO-14** — 2. Tooling Evaluation & Selection *(Parent story, In Progress)*

---

## Context

### Project Setup

| Item | Value |
|------|-------|
| Ansible content | Roles, Collections, Playbooks |
| Source control | GitHub Enterprise (`ecs-ansible-roles`, `ecs-ansible-collections`, `ecs-ansible-playbooks`) |
| CI/CD | GitLab CI (GARM-based pipeline with atoms) |
| GitLab runners | **Dedicated, `privileged: true` enabled** |
| Scale | 199 repos total: 116 roles, 52 playbooks, 31 collections |

### Goal

Evaluate the available Molecule driver options for the E2E test automation setup and select the best fit for GitLab CI:
- Docker
- Podman
- Delegated
- Cloud-based

### Important: Molecule Architecture (2025/2026)

As of current Molecule versions, **Delegated is the default built-in driver**. Docker and Podman are no longer bundled — they are installed as optional plugins via `molecule-plugins`:

```bash
pip install 'molecule-plugins[docker]'   # for Docker (quote to prevent glob expansion)
pip install 'molecule-plugins[podman]'   # for Podman
```

---

## ✅ Decision: Docker

Driver confirmed as **Docker** (`molecule-plugins[docker]`).

**Rationale:**
- Already in use across all **12 active scenarios** in `sap_ecs.configuration_management` — zero migration effort
- `sap_ecs.configuration_management` is the **reference pipeline implementation** per E2ETESTAUTO-9 gap analysis — Docker + GARM Molecule is the established pattern to build on
- GitLab CI runners are **dedicated with `privileged: true`** — the key blocker for Docker doesn't apply here
- Non-privileged containers confirmed in all active Molecule scenarios (no `privileged: true` at platform level)
- Best local dev experience and broadest community/tooling support

**Requirements:**
- GitLab CI runner: `privileged: true` enabled (Docker-in-Docker / DinD)
- `DOCKER_HOST` configured on the runner
- Install: `pip install 'molecule-plugins[docker]'`

**Supersedes:** hybrid Podman/Delegated recommendation — not needed given dedicated runners.

---

## SAP Docker Image Registry

SAP provides Docker images that mimic real VM instances (RHEL + SLES) for use as Molecule platform images:

| Item | Value |
|------|-------|
| Registry | `cia-docker-live.int.repositories.cloud.sap` |
| Package | `multicloud-image-container` |
| UI | https://cia-docker-live.int.repositories.cloud.sap/ui/packages?name=multicloud-image-container&type=packages |
| Execution image | `cia-docker-live.int.repositories.cloud.sap/ansible-molecule-2.18` |

**SLES matrix:** 15 SP1, SP2, SP3, SP4, SP5, SP6, SP7
**RHEL matrix:** 9 SP0, SP4, SP6

---

## Findings

### Docker

- **GitLab CI fit:** Works with dedicated runners + DinD. `privileged: true` required on runner — **confirmed available**.
- **Pros:** Widely documented, fast, in use today, SAP-provided images, best local dev experience.
- **Cons:** Requires privileged runner (not an issue here); DinD adds slight complexity.
- **Current `molecule.yml`:**
  ```yaml
  driver:
    name: docker

  platforms:
    # Minimal container
    - name: instance-minimal
      image: quay.io/centos/centos:stream9
      pre_build_image: true

    # Systemd container (needs privileged + cgroup mount)
    - name: instance-systemd
      image: quay.io/centos/centos:stream8
      privileged: true
      volumes:
        - "/sys/fs/cgroup:/sys/fs/cgroup:rw"
      command: "/usr/sbin/init"
      cgroupns_mode: host
      tty: true
  ```
  > `privileged: true` at platform level is for systemd testing inside the container — separate from the runner privilege requirement.

### Podman

- **Not selected** — no advantage over Docker given dedicated runners.
- Would be relevant if runners were shared/locked-down (rootless, no `privileged: true` needed on runner).

### Delegated

- **Not selected for container-based testing** — relevant for **integration/E2E tests against real VMs** (PTL/VLab) in Phase 2.
- Built-in, no install required.
- Requires authoring `create.yml`/`destroy.yml` with instance-config API.

### Cloud-based

- **Not selected** — high latency, cost per run, requires cloud credentials in CI.
- Phase 2 consideration for full E2E playbook validation against VLab/CAL environments.

---

## Comparison Matrix

| Criterion | Docker | Podman | Delegated | Cloud-based |
|-----------|--------|--------|-----------|-------------|
| GitLab CI — shared runners | ❌ Needs privileged | ✅ Rootless | ✅ No runtime needed | ✅ No runtime needed |
| GitLab CI — dedicated runners | ✅ **Selected** | ✅ | ✅ | ✅ |
| Local dev experience | ✅ Best | ✅ Good | ⚠️ More setup | ❌ Slow/costly |
| Roles testing | ✅ | ✅ | ✅ | ✅ |
| Collections testing | ✅ | ✅ | ✅ | ✅ |
| Playbooks testing | ⚠️ Limited fidelity | ⚠️ Limited fidelity | ✅ Best fit | ✅ Best fit |
| Setup effort | Low | Medium | Medium-High | High |
| Runtime dependency | Docker daemon | Podman binary | None | None + cloud creds |
| SAP/Enterprise security | ⚠️ Privileged concern | ✅ | ✅ | ✅ |
| Already in use at SAP ECS | ✅ 12 scenarios | ❌ | Partial | ❌ |

---

## Open Questions

- [x] ~~Are GitLab CI runners shared or dedicated?~~ → **Dedicated with `privileged: true`**
- [ ] Standardize on CIS images vs. multicloud images — mismatch may cause inconsistencies across repos
- [ ] Verify `SLES_MOLECULE` / `RHEL_MOLECULE` CI/CD variables are enabled in project settings
- [ ] RHEL 8 SP6 in matrix — confirm still needed (nearing EOL)
- [ ] Delegated driver scope for playbook-level / integration tests (Phase 2)

---

## Links & References

- [Molecule official docs — Driver configuration](https://ansible.readthedocs.io/projects/molecule/configuration/)
- [Molecule + GitLab CI guide](https://oneuptime.com/blog/post/2026-02-21-molecule-gitlab-ci/view)
- [Molecule driver system deep-dive (DeepWiki)](https://deepwiki.com/ansible/molecule/5.2-driver-system)
- [molecule-plugins repository](https://github.com/ansible-community/molecule-plugins)
- [SAP multicloud-image-container registry](https://cia-docker-live.int.repositories.cloud.sap/ui/packages?name=multicloud-image-container&type=packages)
- **Related Jira:** [E2ETESTAUTO-55](https://jira.tools.sap/browse/E2ETESTAUTO-55) — SPIKE: Molecule driver selection
- **Parent:** [E2ETESTAUTO-14](https://jira.tools.sap/browse/E2ETESTAUTO-14) — 2. Tooling Evaluation & Selection
