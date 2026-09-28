
# UV

[UV](https://docs.astral.sh/uv/) is a Python package and project manager, written in Rust.

```bash
brew install uv
```

## Ansible with UV

Create a project directory and `cd` into it:

```bash
mkdir -p ~/Projekte/project1
cd ~/Projekte/project1
```

View available and installed Python versions:

```bash
uv python list
```

Install a specific Python version and pin it for this project:

```bash
uv python pin 3.11.16
```

Initialise a bare project (no package structure — for scripting/ops use):

```bash
uv init --bare
```

Create a virtual environment:

```bash
uv venv --python 3.11.16
```

Activate the virtual environment:

```bash
source .venv/bin/activate
```

Check the active Python version:

```bash
python --version
```

Install `ansible-core` into the virtual environment:

```bash
uv add ansible-core
```

Install a specific version:

```bash
uv add ansible-core==2.15.13
```

Check Ansible version:

```bash
ansible --version
```

> [!tip] Config file location
> Note the value of the `config file` line in the output — this tells you which `ansible.cfg` is active for this environment.

#MACOS
