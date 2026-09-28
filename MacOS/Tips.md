
# Keyboard

## Fix: Keys `<>` and `^` Are Swapped

The keyboard is not detected correctly. Remove the detection cache and reboot:

```bash
sudo mv /Library/Preferences/com.apple.keyboardtype.plist /Library/Preferences/com.apple.keyboardtype.plist.bak
sudo reboot
```

After restart: **System Settings → Keyboard → Input Sources** → re-select German.

---

# Keymap

## Windows/Linux → Mac Shell Shortcuts

### Command Execution & Navigation

| Action | Windows/Linux | Mac | Notes |
|--------|---------------|-----|-------|
| **Copy** | `Ctrl+C` | `Cmd+C` | Terminal emulator only |
| **Paste** | `Ctrl+V` | `Cmd+V` | Terminal emulator only |
| **Cut** | `Ctrl+X` | `Cmd+X` | Terminal emulator only |
| **Undo** | `Ctrl+Z` | `Cmd+Z` | Terminal emulator only |
| **Clear Screen** | `Ctrl+L` | `Ctrl+L` | ✓ Same |
| **Cancel Command** | `Ctrl+C` | `Ctrl+C` | ✓ Same — shell level |

> ⚠️ **Ctrl+A / Ctrl+C in the shell**: These are shell commands (move to line start / cancel process). The Karabiner Linux profile remaps them to `Cmd+A`/`Cmd+C` **only outside Terminal/iTerm2**, so shell behaviour is not affected.

### Bash/Shell Line Editing

| Action | Windows/Linux | Mac | Notes |
|--------|---------------|-----|-------|
| **Move to Start of Line** | `Home` / `Ctrl+A` | `Ctrl+A` / `Home` | readline |
| **Move to End of Line** | `End` / `Ctrl+E` | `Ctrl+E` / `End` | readline |
| **Delete Char Forward** | `Del` | `Ctrl+D` | readline |
| **Backspace** | `Backspace` | `Backspace` | ✓ Same |
| **Delete Word Forward** | `Ctrl+Del` | `Alt+D` | readline |
| **Delete Word Backward** | `Ctrl+Backspace` | `Ctrl+W` | readline |
| **History Previous** | `↑` | `↑` | ✓ Same |
| **History Next** | `↓` | `↓` | ✓ Same |
| **Search History** | `Ctrl+R` | `Ctrl+R` | ✓ Same |
| **Transpose Characters** | `Ctrl+T` | `Ctrl+T` | ✓ Same |

### Common Terminal Tasks

| Action | Windows/Linux | Mac | Notes |
|--------|---------------|-----|-------|
| **New Tab** | `Ctrl+T` (varies) | `Cmd+T` | Terminal/iTerm2 |
| **Close Tab** | `Ctrl+W` (varies) | `Cmd+W` | Terminal/iTerm2 |
| **New Window** | `Ctrl+N` (varies) | `Cmd+N` | Terminal/iTerm2 |
| **Previous Tab** | `Ctrl+PageUp` | `Cmd+Shift+[` | Terminal/iTerm2 |
| **Next Tab** | `Ctrl+PageDown` | `Cmd+Shift+]` | Terminal/iTerm2 |
| **Select All** | `Ctrl+A` | `Cmd+A` | Terminal emulator |
| **Fullscreen** | `F11` | `Cmd+Ctrl+F` | Terminal/iTerm2 |

### Important Differences

- **Alt = Option**: Windows `Alt` key is the Mac `Option` key
- **Cmd key**: Most `Ctrl+` shortcuts in GUI apps become `Cmd+` on Mac
- **Alt Codes**: Not supported on Mac — use Unicode or `Cmd+Ctrl+Space` for character palette

### Shell Config: readline Bindings

Add to `~/.zshrc` or `~/.bashrc` for Linux-style key behaviour:

```bash
# Mac-friendly bindings for Linux users
bind '"\e[3~": delete-char'    # Del key — delete forward
bind '"\e[1;5C": forward-word' # Ctrl+Right — move word forward
bind '"\e[1;5D": backward-word'# Ctrl+Left  — move word backward
```

---

## Special Characters — German Keyboard

| Character | Native Shortcut | With Karabiner (Linux profile) |
|-----------|-----------------|-------------------------------|
| **Pipe** `\|` | `Option + <` | `RCmd + `` ` |
| **Tilde** `~` | `Option + N` | `LCtrl + ]` |
| **Backslash** `\` | `Option + Shift + 7` | `RCmd + -` |
| **At** `@` | `Option + L` | `RCmd + Q` |
| **Curly** `{ }` | `Option + 8` / `Option + 9` | `RCmd + 7` / `RCmd + 0` |
| **Brackets** `[ ]` | `Option + 5` / `Option + 6` | `RCmd + 8` / `RCmd + 9` |

> The **Native** column works without any tools. The **Karabiner** column applies when the Linux profile in [[MacOS/Karabiner]] is active.

### German QWERTZ Layout

```
Row 1:  ^ 1 2 3 4 5 6 7 8 9 0 ß ´
Row 2:  Q W E R T Z U I O P Ü +
Row 3:  A S D F G H J K L Ö Ä #
Row 4:  < Y X C V B N M , . -    ← < key left of Y
        ↑ Z is here (not QWERTY)
```

### Set German Keyboard in macOS

1. **System Settings → Keyboard → Input Sources**
2. Click **+** → select **German** → Add
3. Switch layout: `Ctrl+Space` or the flag menu in the menu bar

### Advanced Remapping — Karabiner

```bash
brew install karabiner-elements
```

→ Full config: [[MacOS/Karabiner]]

---

#MACOS
