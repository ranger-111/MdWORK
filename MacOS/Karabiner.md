
# Karabiner-Elements

[Karabiner-Elements](https://karabiner-elements.pqrs.org/) — advanced key remapping for macOS.

```bash
brew install karabiner-elements
```

Config file: `~/.config/karabiner/karabiner.json`

## Linux Profile (German Keyboard)

### Keybinding Summary

| Linux Key | Result | Action |
|-----------|--------|--------|
| **RCmd + -** | `\` | Backslash |
| **RCmd + `** | `\|` | Pipe |
| **LCtrl + ]** | `~` | Tilde |
| **RCmd + Q** | `@` | At-symbol |
| **RCmd + 7** | `{` | Curly brace open |
| **RCmd + 0** | `}` | Curly brace close |
| **RCmd + 8** | `[` | Square bracket open |
| **RCmd + 9** | `]` | Square bracket close |
| **LCtrl + A** | Cmd + A | Select All ⚠️ excluded in Terminal/iTerm2 |
| **LCtrl + C** | Cmd + C | Copy ⚠️ excluded in Terminal/iTerm2 |
| **LCtrl + V** | Cmd + V | Paste |
| **LCtrl + X** | Cmd + X | Cut |

> ⚠️ `LCtrl+A` and `LCtrl+C` are **disabled in Terminal.app and iTerm2** (`frontmost_application_unless` condition), so `Ctrl+A` (move to start of line) and `Ctrl+C` (cancel command) keep their normal shell behaviour inside the terminal.

### karabiner.json

```json
{
    "profiles": [
        {
            "complex_modifications": {
                "rules": [
                    {
                        "description": "German keyboard symbols + Linux shortcuts",
                        "description_notes": [
                            "- RCmd+Hyphen → Option+Shift+7 (\\)",
                            "- RCmd+Grave → Option+7 (|)",
                            "- LCtrl+] → ROption+N (~)",
                            "- RCmd+Q → ROption+L (@)",
                            "- LCtrl+A → LCmd+A (select all, except Terminal/iTerm2)",
                            "- LCtrl+C → LCmd+C (copy)",
                            "- LCtrl+V → LCmd+V (paste)",
                            "- LCtrl+X → LCmd+X (cut)",
                            "- RCmd+8 → Option+5 ([)",
                            "- RCmd+9 → Option+6 (])",
                            "- RCmd+7 → Option+8 ({)",
                            "- RCmd+0 → Option+9 (})"
                        ],
                        "manipulators": [
                            {
                                "from": {
                                    "key_code": "hyphen",
                                    "modifiers": {
                                        "mandatory": ["right_command"],
                                        "optional": ["any"]
                                    }
                                },
                                "to": [
                                    {
                                        "key_code": "7",
                                        "modifiers": ["left_option", "left_shift"]
                                    }
                                ],
                                "type": "basic"
                            },
                            {
                                "from": {
                                    "key_code": "grave_accent_and_tilde",
                                    "modifiers": {
                                        "mandatory": ["right_command"],
                                        "optional": ["any"]
                                    }
                                },
                                "to": [
                                    {
                                        "key_code": "7",
                                        "modifiers": ["left_option"]
                                    }
                                ],
                                "type": "basic"
                            },
                            {
                                "from": {
                                    "key_code": "close_bracket",
                                    "modifiers": {
                                        "mandatory": ["left_control"],
                                        "optional": ["any"]
                                    }
                                },
                                "to": [
                                    {
                                        "key_code": "n",
                                        "modifiers": ["right_option"]
                                    }
                                ],
                                "type": "basic"
                            },
                            {
                                "from": {
                                    "key_code": "q",
                                    "modifiers": {
                                        "mandatory": ["right_command"],
                                        "optional": ["any"]
                                    }
                                },
                                "to": [
                                    {
                                        "key_code": "l",
                                        "modifiers": ["right_option"]
                                    }
                                ],
                                "type": "basic"
                            },
                            {
                                "conditions": [
                                    {
                                        "bundle_identifiers": ["^com\\.apple\\.Terminal$", "^com\\.googlecode\\.iterm2$"],
                                        "type": "frontmost_application_unless"
                                    }
                                ],
                                "from": {
                                    "key_code": "a",
                                    "modifiers": {
                                        "mandatory": ["left_control"],
                                        "optional": ["any"]
                                    }
                                },
                                "to": [
                                    {
                                        "key_code": "a",
                                        "modifiers": ["left_command"]
                                    }
                                ],
                                "type": "basic"
                            },
                            {
                                "conditions": [
                                    {
                                        "bundle_identifiers": ["^com\\.apple\\.Terminal$", "^com\\.googlecode\\.iterm2$"],
                                        "type": "frontmost_application_unless"
                                    }
                                ],
                                "from": {
                                    "key_code": "c",
                                    "modifiers": {
                                        "mandatory": ["left_control"],
                                        "optional": ["any"]
                                    }
                                },
                                "to": [
                                    {
                                        "key_code": "c",
                                        "modifiers": ["left_command"]
                                    }
                                ],
                                "type": "basic"
                            },
                            {
                                "from": {
                                    "key_code": "v",
                                    "modifiers": {
                                        "mandatory": ["left_control"],
                                        "optional": ["any"]
                                    }
                                },
                                "to": [
                                    {
                                        "key_code": "v",
                                        "modifiers": ["left_command"]
                                    }
                                ],
                                "type": "basic"
                            },
                            {
                                "from": {
                                    "key_code": "x",
                                    "modifiers": {
                                        "mandatory": ["left_control"],
                                        "optional": ["any"]
                                    }
                                },
                                "to": [
                                    {
                                        "key_code": "x",
                                        "modifiers": ["left_command"]
                                    }
                                ],
                                "type": "basic"
                            },
                            {
                                "from": {
                                    "key_code": "8",
                                    "modifiers": {
                                        "mandatory": ["right_command"],
                                        "optional": ["any"]
                                    }
                                },
                                "to": [
                                    {
                                        "key_code": "5",
                                        "modifiers": ["left_option"]
                                    }
                                ],
                                "type": "basic"
                            },
                            {
                                "from": {
                                    "key_code": "9",
                                    "modifiers": {
                                        "mandatory": ["right_command"],
                                        "optional": ["any"]
                                    }
                                },
                                "to": [
                                    {
                                        "key_code": "6",
                                        "modifiers": ["left_option"]
                                    }
                                ],
                                "type": "basic"
                            },
                            {
                                "from": {
                                    "key_code": "7",
                                    "modifiers": {
                                        "mandatory": ["right_command"],
                                        "optional": ["any"]
                                    }
                                },
                                "to": [
                                    {
                                        "key_code": "8",
                                        "modifiers": ["left_option"]
                                    }
                                ],
                                "type": "basic"
                            },
                            {
                                "from": {
                                    "key_code": "0",
                                    "modifiers": {
                                        "mandatory": ["right_command"],
                                        "optional": ["any"]
                                    }
                                },
                                "to": [
                                    {
                                        "key_code": "9",
                                        "modifiers": ["left_option"]
                                    }
                                ],
                                "type": "basic"
                            }
                        ]
                    }
                ]
            },
            "name": "Default profile",
            "selected": true,
            "virtual_hid_keyboard": { "keyboard_type_v2": "iso" }
        }
    ]
}
```

#MACOS
