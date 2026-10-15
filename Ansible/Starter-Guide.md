
# Ansible Starter Guide

A quick guide to get Ansible running on macOS using [uv](https://docs.astral.sh/uv/) and run your first playbook.

---

## 1. Install uv

If you don't have uv yet:

```bash
brew install uv
```

---

## 2. Set up a project with uv

Create a project directory and `cd` into it:

```bash
mkdir -p ~/Projekte/ansible-starter
cd ~/Projekte/ansible-starter
```

Pin a Python version and initialise a bare project:

```bash
uv python pin 3.11.16
uv init --bare
```

Create and activate a virtual environment:

```bash
uv venv --python 3.11.16
source .venv/bin/activate
```

Install Ansible:

```bash
uv add ansible-core
```

Verify the installation:

```bash
ansible --version
```

> [!tip] Config file location
> Check the `config file` line in the output — it shows which `ansible.cfg` is active for this environment.

---

## 3. Project structure

Create the following layout:

```
ansible-starter/
├── inventory/
│   └── hosts.ini
├── playbooks/
│   └── hello.yml
└── ansible.cfg
```

```bash
mkdir -p inventory playbooks
```

---

## 4. Configure Ansible

Create `ansible.cfg` in the project root:

```ini
[defaults]
inventory = inventory/hosts.ini
host_key_checking = False
```

---

## 5. Create an inventory

`inventory/hosts.ini` — start with localhost for testing:

```ini
[local]
localhost ansible_connection=local
```

---

## 6. Write your first playbook

`playbooks/hello.yml`:

```yaml
---
- name: Hello World
  hosts: local
  gather_facts: false

  tasks:
    - name: Print a message
      ansible.builtin.debug:
        msg: "Hello from Ansible!"

    - name: Show current date
      ansible.builtin.command: date
      register: current_date

    - name: Print the date
      ansible.builtin.debug:
        var: current_date.stdout
```

---

## 7. Run the playbook

```bash
ansible-playbook playbooks/hello.yml
```

Expected output:

```
PLAY [Hello World] *************************************************************

TASK [Print a message] *********************************************************
ok: [localhost] => {
    "msg": "Hello from Ansible!"
}

TASK [Show current date] *******************************************************
changed: [localhost]

TASK [Print the date] **********************************************************
ok: [localhost] => {
    "current_date.stdout": "Thu Oct 15 10:00:00 CEST 2026"
}

PLAY RECAP *********************************************************************
localhost                  : ok=3    changed=1    unreachable=0    failed=0
```

---

## 8. Useful commands

| Command | Description |
|---|---|
| `ansible all -m ping` | Test connectivity to all hosts |
| `ansible-playbook playbook.yml --check` | Dry run (no changes) |
| `ansible-playbook playbook.yml --diff` | Show file diffs |
| `ansible-playbook playbook.yml -v` | Verbose output |
| `ansible-playbook playbook.yml --tags mytag` | Run only tagged tasks |
| `ansible-doc ansible.builtin.debug` | Show module docs |

---

## See also

- [[MacOS/Python]] — uv setup and Python version management
- [Ansible documentation](https://docs.ansible.com)
- [ansible-core on PyPI](https://pypi.org/project/ansible-core/)

#ANSIBLE
