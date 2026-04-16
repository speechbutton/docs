# Hotkeys & Channels

## How It Works

1. **Hold** a hotkey (default: Right Command)
2. **Speak** while holding
3. **Release** — text appears where your cursor is

## Default Setup

Out of the box, SpeechButton uses **Right Command** as the push-to-talk key:

```toml
[[hotkey]]
name = "default"
key = "RightCommand"
paste = "accessibility"
```

## Channels

Channels let you route speech to different destinations using the same base key. Hold your hotkey, then tap a channel key (1-9, a-z) to activate it.

### Example: 3-channel setup

```toml
# Default: just hold and speak → pastes at cursor
[[hotkey]]
name = "default"
key = "RightCommand"

# Hold key + press 1 → translate to English, then paste
[[hotkey]]
name = "translate"
key = "RightCommand"
channel = "1"
transform = "~/.config/speechbutton/scripts/transform_claude.py ~/.config/speechbutton/prompts/translate_en.md"

# Hold key + press 2 → append to notes file
[[hotkey]]
name = "notes"
key = "RightCommand"
channel = "2"
file = "~/Documents/voice-notes.txt"

# Hold key + press 3 → send to webhook
[[hotkey]]
name = "api"
key = "RightCommand"
channel = "3"
webhook = "http://localhost:8080/voice"
output_format = "json"
```

## Transforms

A transform processes text through a command before output. Text is piped via stdin, the command's stdout becomes the final output.

### Built-in transform scripts

Located in `~/.config/speechbutton/scripts/`:

- `transform_claude.py <prompt_file>` — Claude API (uses Claude Code OAuth)
- `transform_openai.py <prompt_file> [model]` — OpenAI API (uses OPENAI_API_KEY)

### Prompt files

Located in `~/.config/speechbutton/prompts/`:

- `translate_en.md` — translate any language to English
- `cleanup.md` — remove filler words, fix grammar
- `summarize.md` — summarize in 1-2 sentences

### Custom transforms

Any executable that reads stdin and writes stdout:

```toml
transform = "python3 ~/my_script.py"
transform = "sed 's/um //g'"
transform = "tr '[:lower:]' '[:upper:]'"
```

If the transform exits with code != 0, the output is cancelled (text is not pasted/sent).

## Hands-Free Toggle

Short-tap the PTT key (< 300ms) to toggle hands-free mode on/off. This lets you use the same key for both push-to-talk and hands-free.
