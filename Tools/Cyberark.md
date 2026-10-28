---
tags:
  - tools
  - access
  - sap
aliases:
  - CyberArk
  - Jump Host
---

# CyberArk

Zugang zum SAP HEC Jump Host über CyberArk / SecurID.

## Verbindung

```bash
ssh 147.204.148.137
```

Login mit dem eigenen **d-, c- oder i-User**.

## Login-Ablauf

Beim Verbinden werden **zwei** Passwort-Prompts nacheinander abgefragt:

1. **SecurID PASSCODE** am ersten Prompt eingeben
   ```
   (I775185@147.204.148.137) Password:
   ```
2. **SAPHEC-Passwort** am zweiten Prompt eingeben
   ```
   (I775185@147.204.148.137) Password:
   ```

## Nach dem Login

CyberArk-Werkzeuge in die Shell laden:

```bash
source ~/cybr-tools
```

## Host-Eckdaten

| Feld      | Wert                                     |
| --------- | ---------------------------------------- |
| Hostname  | `hec01v000100`                           |
| Kernel    | `6.4.0-150700.53.45-default`             |
| OS        | SLES_SAP 15.7                            |
| CPU       | 8× Intel Xeon E7-8880 v4 @ 2.20 GHz      |
| RAM       | 16 GB (`MemTotal 16343660 kB`)           |
| Swap      | 16 GB                                    |
| eth0      | `147.204.148.137/23`                     |
| eth1      | `100.126.1.36/16`                        |

> [!note]- Login-Banner (Referenz)
> ```
> ################################################################################
> #                                                                              #
> #   _____         _____                                                        #
> #  / ____|  /\   |  __ \   This system is for authorized access only!          #
> # | (___   /  \  | |__) |                                                      #
> #  \___ \ / /\ \ |  ___/   Please login with Your d-, c- or i-user and         #
> #  ____) / ____ \| |                                                           #
> # |_____/_/    \_\_|       Your password.                                      #
> #                                                                   SLES_SAP 15.7 #
> ################################################################################
> -------------------------------------------------------------------------------
> 1) Please enter your SecurID PASSCODE at the password prompt!
> -------------------------------------------------------------------------------
> 2) Please enter your SAPHEC password at the following password prompt!
> -------------------------------------------------------------------------------
> ```

> [!warning]- Rechtlicher Hinweis (SAP Systems)
> ATTENTION – UNAUTHORIZED ACCESS IS PROHIBITED
>
> You are accessing SAP computer systems and network through which you may have
> access to business sensitive information ("SAP Systems"). SAP Systems are intended
> for legitimate business use by authorized users of SAP only. Personal use of the
> SAP Systems you are about to access is not permitted. Unless applicable law
> provides otherwise, (i) any information stored in, transmitted through or
> processed by SAP Systems remains at all times the property of SAP, (ii) SAP has
> the right to scan and monitor access to and use of SAP Systems, and (iii) any
> information on SAP Systems and any processing activities relating to such
> information can be inspected by SAP at any time. Except as may be provided by
> applicable law, you should have no expectation of privacy as to any information
> stored in, transmitted through or processed by any portion of SAP Systems.
> Unauthorized access or use of SAP Systems may result in disciplinary and legal action.
