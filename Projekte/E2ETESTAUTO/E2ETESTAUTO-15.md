# E2ETESTAUTO-15 — Evaluate Molecule Drivers

## Jira Metadata

| Field        | Value                                                                   |
| ------------ | ----------------------------------------------------------------------- |
| Key          | [E2ETESTAUTO-15](https://jira.tools.sap/browse/E2ETESTAUTO-15)          |
| Summary      | 2.1- Evaluate Molecule drivers (Docker, Podman, delegated, cloud-based) |
| Type         | Task                                                                    |
| Status       | In Progress                                                             |
| Priority     | Medium                                                                  |
| Assignee     | Maik Smuda                                                              |
| Reporter     | Diana Simina                                                            |
| Created      | 2026-07-10                                                              |
| Last Updated | 2026-09-29                                                              |

## Status History

| Status | Duration |
|--------|----------|
| Open | 81d 4h 53m (2026-07-10 → 2026-09-29) |
| In Progress | since 2026-09-29 (moved by Diana Simina) |

## Related Issues

- **E2ETESTAUTO-55** — 2.5- SPIKE: Molecule driver selection — Docker vs. Podman vs. delegated *(Open, same assignee)*

## Context

### Project Setup

| Item | Value |
|------|-------|
| Ansible content | Roles, Collections, Playbooks |
| Source control | GitHub Enterprise |
| CI/CD | GitLab CI |

### Goal

Evaluate the available Molecule driver options for the E2E test automation setup and select the best fit for GitLab CI:
- Docker
- Podman
- Delegated
- Cloud-based

### Important: Molecule Architecture (2025/2026)

As of current Molecule versions, **Delegated is the default built-in driver**. Docker and Podman are no longer bundled — they are installed as optional plugins via `molecule-plugins`:

```bash
pip install molecule molecule-plugins[docker]   # for Docker
pip install molecule molecule-plugins[podman]   # for Podman
```

This means the architectural decision is: **use a container plugin (Docker/Podman) or implement provisioning yourself via delegated**.

---

### Findings

#### Docker

- **GitLab CI fit:** Works with Docker executor + Docker-in-Docker (DinD). Requires **privileged mode** on the runner — a significant security consideration in shared/enterprise environments.
- **Pros:** Widely documented, fast, broad community support. Best for local dev.
- **Cons:** DinD requires privileged containers; less suitable for locked-down or shared GitLab runners.
- **Install:** `pip install molecule molecule-plugins[docker]`
- **GitLab CI requirement:** Runner with `privileged: true` and `DOCKER_HOST` configured.
- **SAP-provided images:** SAP provides Docker images that mimic real VM instances — **RHEL** and **SLES**. These are used directly as Molecule platform images, ensuring test fidelity without custom image builds.
  - **Registry:** `cia-docker-live.int.repositories.cloud.sap`
  - **Package:** `multicloud-image-container`
  - **UI:** [https://cia-docker-live.int.repositories.cloud.sap/ui/packages?name=multicloud-image-container&type=packages](https://cia-docker-live.int.repositories.cloud.sap/ui/packages?name=multicloud-image-container&type=packages)
  - ⚠️ **Images werden regelmäßig aktualisiert — Image-Tags immer vor Verwendung in der Registry prüfen. Keine festen Tags in `molecule.yml` hardcoden; stattdessen den aktuellen Tag aus der Registry ermitteln (z. B. via CI-Step oder Wrapper-Script).**

#### Podman

- **GitLab CI fit:** Strong fit for enterprise/rootless environments. Runs **without a daemon** and supports rootless containers — no `privileged: true` needed on the runner.
- **Pros:** Rootless, SELinux-friendly, no Docker daemon dependency. Increasingly the default on RHEL-family systems.
- **Cons:** Slightly more setup variance than Docker; some tooling still assumes Docker conventions.
- **Install:** `pip install molecule molecule-plugins[podman]` + `ansible-galaxy collection install containers.podman`
- **GitLab CI requirement:** Podman available on runner image (or use a Podman-based CI image).

#### Delegated

- **GitLab CI fit:** Most flexible. Molecule orchestrates `molecule test`, GitLab runs it — no driver dependency at all. You implement `create.yml` and `destroy.yml` in Ansible.
- **Pros:** Fully Ansible-native, no container runtime dependency, works against any infrastructure (VMs, cloud, bare metal). Ideal for testing **playbooks** against realistic targets.
- **Cons:** Must author and maintain `create.yml`/`destroy.yml`. More boilerplate upfront.
- **Install:** Built-in — no extra packages.
- **GitLab CI requirement:** None beyond a standard runner.

#### Cloud-based

- **GitLab CI fit:** Variant of delegated — `create.yml` provisions cloud VMs (AWS, GCP, Azure) and `destroy.yml` tears them down. Highest fidelity but adds cost and latency.
- **Pros:** Most realistic test targets, good for full playbook validation.
- **Cons:** Slow (VM boot time), cost per test run, requires cloud credentials in CI. Overkill for role unit tests.
- **Install:** Built-in delegated + relevant Ansible cloud collection.

---

### Comparison Matrix

| Criterion | Docker | Podman | Delegated | Cloud-based |
|-----------|--------|--------|-----------|-------------|
| GitLab CI — shared runners | ❌ Needs privileged | ✅ Rootless | ✅ No runtime needed | ✅ No runtime needed |
| GitLab CI — dedicated runners | ✅ | ✅ | ✅ | ✅ |
| Local dev experience | ✅ Best | ✅ Good | ⚠️ More setup | ❌ Slow/costly |
| Roles testing | ✅ | ✅ | ✅ | ✅ |
| Collections testing | ✅ | ✅ | ✅ | ✅ |
| Playbooks testing | ⚠️ Limited fidelity | ⚠️ Limited fidelity | ✅ Best fit | ✅ Best fit |
| Setup effort | Low | Medium | Medium-High | High |
| Runtime dependency | Docker daemon | Podman binary | None | None + cloud creds |
| SAP/Enterprise security | ⚠️ Privileged concern | ✅ | ✅ | ✅ |

---

### Decision

**✅ Decision: Docker — confirmed 2026-10-07**

Driver confirmed as **Docker** (`molecule-plugins[docker]`). Rationale:

- Already in use across **all 12 active scenarios** in `sap_ecs.configuration_management` — zero migration effort.
- `sap_ecs.configuration_management` is explicitly the **reference pipeline implementation** per E2ETESTAUTO-9 gap analysis — Docker + GARM Molecule is the established pattern to build on.
- GitLab CI runners are dedicated and support `privileged: true` (required for Docker-in-Docker).
- Non-privileged containers confirmed in all active molecule scenarios (no `privileged: true` at platform level in `sap_ecs.configuration_management`).
- Best local dev experience and broadest community/tooling support.

**Requirements:**
- GitLab CI runner: `privileged: true` enabled (Docker-in-Docker / DinD).
- `DOCKER_HOST` configured on the runner.
- Install via: `pip install 'molecule-plugins[docker]'`

**Supersedes:** hybrid Podman/Delegated recommendation — not needed given dedicated runners.

### Open Questions

- [x] ~~Are GitLab CI runners shared or dedicated? Do they support `privileged: true`?~~ → Dedicated runners confirmed, DinD supported.
- [x] ~~Is there an existing Molecule setup to build on, or greenfield?~~ → `sap_ecs.configuration_management` has 12 active Docker scenarios — build on that.
- [x] ~~Any security/compliance policy on privileged containers at SAP?~~ → No `privileged: true` at platform level in existing scenarios; runner-level DinD is accepted.
- [x] ~~Is the Oct 6 handover meeting a deadline for this decision?~~ → Meeting passed (2026-10-06); decision made 2026-10-07.
- [x] ~~What target OS images are needed for test instances?~~ → SAP-provided Docker images mimicking real VM instances: **RHEL** and **SLES**. No custom build required.

## Latest Docs — Context7 (2026-09-30)

> Sources: `/ansible/molecule` (Score 84, High reputation) · `/ansible-community/molecule-plugins` (Score 73, High reputation)

### Confirmed: Driver Architecture

Molecule's **Ansible-native approach** (current default) uses `driver: name: default` (also aliased as `de`). Docker and Podman are external plugins installed via `molecule-plugins`.

```bash
# Single driver
pip install 'molecule-plugins[docker]'
pip install 'molecule-plugins[podman]'

# Multiple at once
pip install 'molecule-plugins[docker,podman,ec2]'
```

> ⚠️ Note: Quotes around `molecule-plugins[...]` are required in most shells to prevent glob expansion.

---

### Docker Driver — Current molecule.yml

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

> **GitLab CI note:** `privileged: true` at the *platform level* is for systemd testing inside the container — but GitLab CI still requires the **runner** to be privileged for Docker-in-Docker. Both layers need privilege consideration.

---

### Podman Driver — Current molecule.yml

```yaml
driver:
  name: podman

platforms:
  # Rootless — no privileged runner needed
  - name: instance-basic
    image: quay.io/fedora/fedora:39
    pre_build_image: true

  # Systemd container (privileged at container level only)
  - name: instance-systemd
    image: centos:8
    privileged: true
    command: "/usr/sbin/init"
    systemd: true

  # Advanced options
  - name: instance-advanced
    image: registry.example.com/myapp:tag
    volumes:
      - "/sys/fs/cgroup:/sys/fs/cgroup:ro"
    security_opts:
      - "label=disable"
    cgroup_manager: cgroupfs
    storage_driver: overlay
    tmpfs:
      - /tmp
      - /run
```

> Podman driver uses the `containers.podman` Ansible collection. Containers are labeled `owner=molecule` and auto-cleaned on `molecule destroy` / `molecule reset`.

---

### Delegated Driver — Current Config & Instance-Config API

```yaml
driver:
  name: default   # or: name: de
```

The `create.yml` must output this instance-config structure (for SSH targets):

```yaml
- address: ssh_endpoint
  identity_file: ssh_identity_file  # mutually exclusive with password
  instance: instance_name
  port: ssh_port_as_string
  user: ssh_user
  shell_type: sh
  become_method: sudo               # optional
  become_pass: password_if_required # optional
```

Custom provisioner playbooks:

```yaml
provisioner:
  name: ansible
  playbooks:
    create: create.yml
    converge: converge.yml
    destroy: destroy.yml
```

For unmanaged (pre-existing) instances (no create/destroy needed):

```yaml
driver:
  name: default
  options:
    managed: False
    ansible_connection_options:
      ansible_connection: local   # or ssh, docker, etc.
```

---

### Docs-Confirmed Changes vs. Note Findings

| Topic | Note says | Docs confirm |
|-------|-----------|-------------|
| Delegated driver name | `name: de` | ✅ Both `de` and `default` work |
| Docker install | `pip install molecule molecule-plugins[docker]` | ✅ Correct (add quotes: `'molecule-plugins[docker]'`) |
| Podman rootless | "no privileged needed on runner" | ✅ Confirmed — basic containers run rootless |
| Podman systemd | Needs privileged | ✅ `privileged: true` at platform level, not runner level |
| Delegated: create/destroy required | ✅ | ✅ Must follow instance-config API exactly |
| molecule-plugins EC2 | "Cloud-based via delegated" | ➕ `molecule-plugins[ec2]` exists as a direct plugin option |

---

### Links & References

- [Molecule official docs — Driver configuration](https://ansible.readthedocs.io/projects/molecule/configuration/)
- [Molecule + GitLab CI guide](https://oneuptime.com/blog/post/2026-02-21-molecule-gitlab-ci/view)
- [Molecule + Podman in GitLab CI (Stack Overflow)](https://stackoverflow.com/questions/79292207/integration-of-ansible-molecule-with-podman-in-gitlab-ci-cd-runner-context)
- [Molecule driver system deep-dive (DeepWiki)](https://deepwiki.com/ansible/molecule/5.2-driver-system)
- [molecule-plugins repository](https://github.com/ansible-community/molecule-plugins)
- **Related Jira:** [E2ETESTAUTO-55](https://jira.tools.sap/browse/E2ETESTAUTO-55) — SPIKE: Molecule driver selection (Docker vs. Podman vs. delegated)
