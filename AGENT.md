
# AGENT.md

> [!important] This is not a knowledge note
> This file is **agent configuration** — instructions and context for LLM agents working in this vault. It is versioned in git alongside the vault but is not part of the knowledge base. Do not link to it from content notes.

Instructions and context for LLM agents working in this Obsidian vault.

---

## Vault Purpose

Personal knowledge base for a developer working primarily on macOS with a German QWERTZ keyboard. Focus areas:
- macOS setup, tooling, shell configuration
- Software development projects (currently SWIFT/SAP)
- Technical reference notes and how-tos

---

## Folder Structure

| Folder / File | Purpose |
|---------------|---------|
| `Inbox.md` | Raw capture — ideas, links, and misc that haven't been processed yet |
| `MacOS/` | macOS tips, keyboard setup, installed tools, shell config |
| `Projekte/` | One subfolder per project, e.g. `Projekte/SWIFT/` |

**Conventions:**
- New topics get their own file, not a giant catch-all note
- Related notes stay in the same folder — cross-link with `[[wikilinks]]`
- If a section in a note grows large, split it into its own file and link back

---

## Note Conventions

**Tags** — placed on the **last line** of the file, uppercase, e.g.:
```
#MACOS #SWIFT #Projekte
```

**No YAML frontmatter** — tags go inline, not in a front matter block.

**Headings** — `#` for page title, `##` for main sections, `###` for subsections.

**Code blocks** — always specify the language (`bash`, `json`, `swift`, etc.).

**Links** — use Obsidian wikilinks: `[[MacOS/Tips]]`, `[[Projekte/SWIFT/Notes]]`.

**Language** — notes are written in **English**.

---

## User Context

- **OS**: macOS (Darwin), Apple Silicon
- **Keyboard**: German QWERTZ layout on Mac
  - Pipe `|` → `Option + <`
  - Tilde `~` → `Option + N`
  - Backslash `\` → `Option + Shift + 7`
  - Full keymap: [[MacOS/Tips]], remapping: [[MacOS/Karabiner]]
- **Shell**: zsh
- **Package manager**: Homebrew → [[MacOS/Homebrew]]
- **Key tools**: Karabiner-Elements, iTerm2/Terminal, gh (GitHub CLI)

---

## Agent Behaviour

### When creating or editing notes

- Match the existing style of the file being edited
- Keep notes **concise** — prefer tables and code blocks over prose
- Do **not** add YAML frontmatter unless asked
- Do **not** create README or documentation files unless asked
- Place tags on the last line, no trailing blank line after tags
- Prefer splitting large sections into linked sub-notes over making one long note

### When asked to improve a note

- Point out issues first and ask for confirmation before rewriting
- Preserve the user's own wording and structure where possible
- Remove redundancy, fix inconsistencies, tighten prose

### When unsure about folder or filename

- Ask before creating new folders
- Follow the existing pattern: `Projekte/<NAME>/Notes.md` for projects, `<Topic>/<Subtopic>.md` for reference
- Raw captures (ideas, links, misc) that haven't been filed yet belong in `Inbox.md` at the vault root

### What NOT to do

- Do not guess key combinations — the German keyboard layout is non-obvious; check [[MacOS/Tips]] first
- Do not add emoji unless asked
- Do not pad notes with headers like "Overview", "Introduction", "Conclusion"

---

## Available Tools

### Obsidian CLI
The `obsidian` CLI is enabled and available. Prefer it over manual file reads for vault-wide operations:

```bash
obsidian tags sort=count counts     # audit tags
obsidian orphans                    # find unlinked notes
obsidian unresolved                 # find broken links
obsidian files                      # list all notes
obsidian search query="..."         # search vault content
```

### Git
The vault is a git repository. Use standard `git` commands for history and diffs. The `.gitignore` excludes `workspace.json`, cache, and `.claudian/sessions/`.

### Skills
The following Claudian skills are installed and should be used proactively:

| Skill | Use for |
|-------|---------|
| `obsidian-cli` | Vault operations, auditing, plugin dev |
| `obsidian-markdown` | Callouts, embeds, wikilinks, properties |
| `obsidian-bases` | Creating `.base` database views |
| `json-canvas` | Creating `.canvas` visual maps |
| `defuddle` | Extracting clean Markdown from web pages |
| `knap` | Generating notes from templates and data |
