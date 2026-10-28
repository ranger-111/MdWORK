---
tags:
  - tools
  - access
  - sap
  - cyberark
aliases:
  - CyberArk
  - Jump Host
  - cybr-tools
---

# CyberArk

Privilegierter Zugang zu SAP-HEC-Hosts über CyberArk **SIA** (Secure Infrastructure Access).

- **Jump Host**: erster Einstieg per SSH mit SecurID + SAPHEC-Passwort.
- **cybr-tools**: Helper-Funktionen, um von dort per `cybr-ssh` / `cybr-sftp` / `cybr-scp` über das CyberArk-Gateway auf die Zielsysteme zu verbinden.
- Repo: [ECSTECHOPSSRE/cybr-tools](https://github.tools.sap/ECSTECHOPSSRE/cybr-tools)
- Doku: [CyberArk LTS (SAP Wiki)](https://wiki.one.int.sap/wiki/pages/releaseview.action?pageId=6354307023)

## 1. Jump Host Login

```bash
ssh 147.204.148.137   # LTS-Node 1
ssh 147.204.148.179   # LTS-Node 2
```

> [!warning] Vorübergehend per IP verbinden
> Die CyberArk-LTS-Hostnamen sind vom Laptop aktuell **nicht auflösbar** (DNS in Arbeit) – bis dahin die IPs direkt nutzen: `147.204.148.137` und `147.204.148.179`.
> **HA mit Load Balancer** ist ebenfalls in Arbeit. Sobald aktiv, wird der LB-Hostname im [SAP Wiki](https://wiki.one.int.sap/wiki/pages/releaseview.action?pageId=6354307023) nachgetragen.

Login mit dem eigenen **d-, c- oder i-User**. Es folgen **zwei** Passwort-Prompts:

1. **SecurID PASSCODE** am ersten Prompt
2. **SAPHEC-Passwort** am zweiten Prompt

```
(I775185@147.204.148.137) Password:   # 1) SecurID PASSCODE
(I775185@147.204.148.137) Password:   # 2) SAPHEC-Passwort
```

## 2. cybr-tools laden

```bash
source ~/cybr-tools
```

Lädt die Helper-Funktionen in die Shell (oder dauerhaft via `~/.bashrc` / `~/.zshrc`).

### Befehlsübersicht

| Befehl | Beschreibung |
| --- | --- |
| `cybr-login` | CyberArk-SSH-Zertifikat holen |
| `cybr-ssh` | SSH-Session über CyberArk öffnen |
| `cybr-sso` | `cybr-login` + `cybr-ssh` in einem Schritt |
| `cybr-sftp` | Interaktive SFTP-Session über CyberArk |
| `cybr-scp` | Dateitransfer über CyberArk SCP |
| `cybr-sso-sftp` | `cybr-login` + `cybr-sftp` |
| `cybr-sso-scp` | `cybr-login` + `cybr-scp` |
| `cybr-hosts-add` | Host-Alias registrieren (Kurzname → FQDN) |
| `cybr-hosts-list` | Alle Aliase anzeigen |
| `cybr-hosts-forget` | Einzelnen Alias entfernen |
| `cybr-hosts-clear` | Alias-Datei löschen |
| `cybr-help` | Befehlsreferenz anzeigen |

### Verbindungsstring

```
user@login@target     # volle Form, z.B. your.mail@sap.com@hec99vXXXXXX
target                # Kurzform (nutzt CYBR_DEFAULT_USER)
```

### Typische Nutzung

```bash
# Zertifikat holen (einmal pro Gültigkeitszeitraum)
cybr-login

# Verbinden (Standard-User)
cybr-ssh hec99vXXXXXX

# Login + Verbinden in einem Schritt
cybr-sso your.mail@sap.com@hec99vXXXXXX

# Zusätzliche SSH-Flags nach --
cybr-ssh hec99vXXXXXX -- -L 8080:localhost:80

# SFTP / SCP
cybr-sftp hec99vXXXXXX
cybr-scp hec99vXXXXXX:/remote/path ./local      # Download
cybr-scp ./local hec99vXXXXXX:/remote/path      # Upload

# Trockenlauf (Befehl anzeigen, nicht verbinden)
cybr-ssh --dry-run hec99vXXXXXX
```

## 3. Setup (bash/zsh)

> [!important] Config zuerst anlegen
> `~/.cybr/config` ist Pflicht, bevor die Tools funktionieren – aus dem Example erzeugen.

```bash
mkdir -p ~/.cybr
cp cybr-config.bash.example ~/.cybr/config
```

Pflichtwerte in `~/.cybr/config`:

```bash
CYBR_DEFAULT_USER="your.mail@sap.com"
CYBR_TENANT="sap.cyberark.cloud"
CYBR_SSH_HOST="sap.ssh.cyberark.cloud"
```

Dann in `~/.bashrc` / `~/.zshrc`:

```bash
source /pfad/zu/cybr-tools
```

## Hostname-Auflösung

Kurznamen werden in dieser Reihenfolge zu FQDNs aufgelöst:

1. **FQDN** (enthält Punkt) → unverändert
2. **Alias** (via `cybr-hosts-add`)
3. **Location-Map** → `<host>.<middle>.hec.sap.biz` (Präfix aus Hostname → Region)
4. **DNS-Verifikation** des gebauten FQDN

Nicht abgedeckte Hosts registrieren:

```bash
cybr-hosts-add myhost myhost.custom.domain.com
```

> [!tip]- Location-Map (Präfix → Region)
> `hec01=rot` · `hec02=ams` · `hec03=stl` · `hec04=sac` · `hec05=tyo` · `hec06=osa`
> `hec07=syd` · `hec08=syd2` · `hec09=rot2` · `hec10=wdf` · `hec11=mow` · `hec13=yyz`
> `hec15=fra` · `hec16=ash` · `hec42=irl` · `hec50=sao` · `hec51=mum` · `hec53=sin`
> `hec60=chn1` · `hec98=hlab` · `hec99=lab` …
> Vollständige Liste in `~/.cybr/config` / Repo.

## Hinweise

- `cybr-config.bash` / `.ps1` enthalten persönliche Credentials → **nie committen** (sind gitignored).
- `ssh`, `sftp`, `scp` müssen im PATH sein.
- SSH nutzt immer `-o IdentitiesOnly=yes -o IdentityAgent=none` (verhindert Agent-Interferenz).
- Auch für **PowerShell** (Windows/Citrix) verfügbar – Details im Repo-README.

---


> [!note]- Login-Banner (Referenz)
> ```
> ################################################################################
> #   _____         _____                                                        #
> #  / ____|  /\   |  __ \   This system is for authorized access only!          #
> # | (___   /  \  | |__) |                                                      #
> #  \___ \ / /\ \ |  ___/   Please login with Your d-, c- or i-user and         #
> #  ____) / ____ \| |                                                           #
> # |_____/_/    \_\_|       Your password.                          SLES_SAP 15.7 #
> ################################################################################
> 1) Please enter your SecurID PASSCODE at the password prompt!
> 2) Please enter your SAPHEC password at the following password prompt!
> ```

