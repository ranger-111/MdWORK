---
tags:
  - tools
  - playwright
  - sap
  - sso
  - automation
aliases:
  - Playwright
  - Headful SSO Login
  - Authenticated Scrape
---

# Playwright — authentifizierte SAP-Seiten auslesen

Technik, um eine **SSO-geschützte interne Seite** (z. B. `wiki.one.int.sap` / Confluence) programmatisch auszulesen, wenn `WebFetch` & Co. an der Login-Wall scheitern. Kern­idee: **frischer Browser + manueller Login durch den Menschen**, danach liest das Skript den gerenderten Inhalt.

Erstmals genutzt am 2026-11-11, um die Seite *GARM 2.0* (Space CIEA) für [[E2ETESTAUTO-72]] auszulesen.

## Warum nicht einfacher?

> [!warning] Drei Sackgassen, die wir ausgeschlossen haben
> - **`WebFetch`**: bekommt nur die SSO-/Login-Seite zurück, nicht den Inhalt.
> - **Profil kopieren** (Cookies aus dem echten Chrome-Profil klauen): auf macOS durch **TCC** blockiert — `Operation not permitted` beim Lesen von `~/Library/Application Support/Google/Chrome/…`, selbst mit deaktivierter Shell-Sandbox. Bräuchte „Full Disk Access" für die Host-App.
> - **An laufendes Chrome andocken (CDP)**: Chrome **≥ 136** ignoriert `--remote-debugging-port` beim **Standard-Profil** (Schutz gegen Cookie-Diebstahl). Geht nur mit separatem `user-data-dir`.

## Der funktionierende Weg

Ein **frisches, leeres** Chrome-Profil in einem Temp-Verzeichnis, **headful** gestartet, Mensch loggt sich per SSO ein, Skript pollt bis der Inhalt da ist und extrahiert ihn.

- **`channel="chrome"`** → nutzt das installierte Google Chrome, **kein** Chromium-Download nötig.
- Eigenes `user-data-dir` im Temp → läuft **parallel** zum normalen Chrome, kein Profil-Lock.
- Läuft **ephemer über `uv`**, nichts wird dauerhaft installiert.

### Ausführen

```bash
mkdir -p /tmp/pw-capture && cd /tmp/pw-capture
uv run --with playwright python grab.py
```

> [!tip] `uv` statt `pip`/`npm`
> `uv run --with playwright …` zieht das Python-Paket nur für diesen Lauf. Mit `channel="chrome"` entfällt `playwright install` (kein Browser-Download).

### Skript (`grab.py`)

```python
import sys, time, pathlib
from playwright.sync_api import sync_playwright

URL     = "https://wiki.one.int.sap/wiki/spaces/CIEA/pages/5835484377/GARM+2.0"
MATCH   = "5835484377"            # Teil der Ziel-URL, der "eingeloggt & da" markiert
OUT     = pathlib.Path("/tmp/pw-capture/out.txt")
UDD     = "/tmp/pw-capture/udd"   # frisches, leeres Profil
DEADLINE = time.time() + 540      # ~9 min Zeit zum Einloggen

SNAP = """() => {
  const sel = document.querySelector('#main-content, .wiki-content, #content');
  return { url: location.href, title: document.title,
           textLen: (document.body ? document.body.innerText.length : 0),
           hasContent: !!sel };
}"""
EXTRACT = """() => (document.querySelector('#main-content')
  || document.querySelector('.wiki-content')
  || document.querySelector('#content')
  || document.body).innerText"""

with sync_playwright() as p:
    ctx = p.chromium.launch_persistent_context(
        UDD, channel="chrome", headless=False,
        args=["--no-first-run", "--no-default-browser-check"])
    page = ctx.pages[0] if ctx.pages else ctx.new_page()
    try:
        page.goto(URL, wait_until="domcontentloaded", timeout=60000)
    except Exception as e:
        print("GOTO_WARN:", e, flush=True)
    print("BROWSER_OPEN: jetzt im neuen Fenster per SSO anmelden ...", flush=True)

    captured = None
    while time.time() < DEADLINE:
        for pg in list(ctx.pages):              # über alle Tabs iterieren
            try: s = pg.evaluate(SNAP)
            except Exception: continue
            if (MATCH in s["url"] and "wiki.one.int.sap" in s["url"]
                    and s["hasContent"] and s["textLen"] > 500):
                page, captured = pg, s; break
        if captured: break
        time.sleep(3)

    if not captured:
        print("TIMEOUT_NO_CONTENT", flush=True); ctx.close(); sys.exit(2)

    try: page.wait_for_load_state("networkidle", timeout=15000)
    except Exception: pass
    OUT.parent.mkdir(parents=True, exist_ok=True)
    OUT.write_text("TITLE: %s\nURL: %s\n\n%s"
                   % (page.title(), captured["url"], page.evaluate(EXTRACT)),
                   encoding="utf-8")
    print("CAPTURED_OK: chars=%d -> %s" % (len(OUT.read_text()), OUT), flush=True)
    ctx.close()
```

> [!note] Ablauf
> 1. Skript im **Hintergrund** starten (Terminal frei halten).
> 2. Im neu geöffneten Chrome-Fenster per **SAP-SSO** anmelden (inkl. MFA).
> 3. Sobald die Zielseite geladen ist, erkennt das Poll-Loop den Inhalt, schreibt `out.txt` und beendet sich.

## Anpassen für andere Seiten

- `URL` und `MATCH` setzen (`MATCH` = eindeutiger URL-Bestandteil der Zielseite).
- Content-Selektoren in `SNAP`/`EXTRACT` ggf. anpassen (Confluence: `#main-content` / `.wiki-content`).
- Für mehrere Seiten: das `UDD` **wiederverwenden** → Login bleibt bestehen, kein erneutes SSO.

## Gotchas

- **Headful ist Pflicht** (`headless=False`) — der Mensch muss interaktiv einloggen können.
- Über **alle `ctx.pages`** pollen, falls SSO einen neuen Tab/Popup öffnet.
- **Poll statt fixem Sleep**: Login-Dauer ist unbekannt; Deadline großzügig wählen.
- Erster `uv`-Lauf lädt das Playwright-Wheel (~46 MB) einmalig; danach gecached.

## Aufräumen

> [!important] Temp-Profil enthält gültige SSO-Cookies
> Nach dem Auslesen hält `UDD` (`/tmp/pw-capture/udd`) eine **aktive SAP-Session**. Wenn nicht mehr gebraucht:
> ```bash
> rm -rf /tmp/pw-capture
> ```

## Siehe auch

- [[Cyberark]] — anderer SAP-Zugangsweg (privilegierte Hosts)
