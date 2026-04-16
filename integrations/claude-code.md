# Claude Code Integration

Use SpeechButton to talk to [Claude Code](https://docs.anthropic.com/en/docs/claude-code) — voice-driven AI coding.

## Setup

```toml
[[hotkey]]
name = "claude-code"
key = "RightCommand"
channel = "5"
transform = "~/.config/speechbutton/scripts/transform_claude.py ~/.config/speechbutton/prompts/cleanup.md"
exec = "claude -p --bare"
```

**How it works:**
1. Hold Right Command + press 5
2. Speak your coding instruction
3. Release — speech is cleaned up (filler words removed), then piped to `claude -p --bare`
4. Claude Code executes your instruction

## Without cleanup (raw dictation to Claude)

```toml
[[hotkey]]
name = "claude-raw"
key = "RightCommand"
channel = "5"
exec = "claude -p --bare"
```

## Tips

- Use the `cleanup.md` transform to remove "um", "uh", "like" before sending to Claude
- Works with any Claude Code session — the `exec` command pipes text to stdin
- Combine with `output_format = "json"` if you want structured metadata alongside the text
