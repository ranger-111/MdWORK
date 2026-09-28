
# AGENT.md

Instructions and context for LLM agents working in this Obsidian vault.

---

## Vault Purpose

Personal knowledge base for a developer working primarily on macOS with a German QWERTZ keyboard. Focus areas:
- macOS setup, tooling, shell configuration
- Software development projects (currently SWIFT/SAP)
- Technical reference notes and how-tos

---

## Folder Structure

| Folder | Purpose |
|--------|---------|
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

### What NOT to do

- Do not guess key combinations — the German keyboard layout is non-obvious; check [[MacOS/Tips]] first
- Do not add emoji unless asked
- Do not pad notes with headers like "Overview", "Introduction", "Conclusion"

---

## Key Reference Files

| File | Contents |
|------|----------|
| [[MacOS/Tips]] | Shell keymap, German keyboard shortcuts, special characters |
| [[MacOS/Karabiner]] | Karabiner-Elements config (Linux key profile) |
| [[MacOS/Homebrew]] | Installed Homebrew packages and casks |
| [[Projekte/SWIFT/Notes]] | SAP SWIFT project notes |

#AGENT
