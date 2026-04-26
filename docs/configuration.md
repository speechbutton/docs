# Configuration Reference

SpeechButton is configured via a single TOML file:

```
~/.config/speechbutton/config.toml
```

Changes are hot-reloaded — edit the file and SpeechButton picks up changes within seconds, no restart needed.

## Global Settings

```toml
# STT model to use (see Models doc for full list)
model = "parakeet-tdt-0.6b-v3-int8"

# Language: "auto" or ISO code ("en", "ru", "de", etc.)
language = "auto"

# Add period at end of sentences if missing
auto_punctuation = true

# Input microphone (empty = system default)
input_device = ""
```

## Hotkeys

Each `[[hotkey]]` block defines a key binding with its own output routing:

```toml
[[hotkey]]
name = "default"
key = "RightCommand"        # Hold this key to record
# channel = "1"             # Optional: press 1 while holding key
paste = "accessibility"     # Output: paste at cursor
# transform = "..."         # Optional: process text before output
# output_format = "text"    # "text" or "json"
```

### Available Keys

`RightCommand`, `LeftCommand`, `RightShift`, `LeftShift`, `RightOption`, `LeftOption`, `RightControl`, `LeftControl`, `F13`–`F20`, `CapsLock`

### Output Types

Each hotkey supports ONE primary output (choose one):

| Output | Config | Description |
|--------|--------|-------------|
| Paste | `paste = "accessibility"` | Insert text where the cursor is (default) |
| Clipboard | `paste = "clipboard"` | Copy to clipboard only, user pastes manually |
| File | `file = "~/notes.txt"` | Append each transcription as a new line |
| Webhook | `webhook = "http://..."` | HTTP POST with JSON body |
| Exec | `exec = "command"` | Pipe text to a shell command via stdin |

### Transform

Runs BEFORE output — text is piped through the command (stdin -> stdout):

```toml
# Claude API transform (uses Claude Code OAuth)
transform = "~/.config/speechbutton/scripts/transform_claude.py ~/.config/speechbutton/prompts/translate_en.md"

# OpenAI API transform
transform = "~/.config/speechbutton/scripts/transform_openai.py ~/.config/speechbutton/prompts/cleanup.md gpt-4o-mini"

# Any custom script
transform = "python3 ~/my_transform.py"
```

### Channels

Channels let you create multiple output destinations from one hotkey:

```toml
# Default: just hold Right Command and speak
[[hotkey]]
name = "default"
key = "RightCommand"

# Channel 1: hold Right Command, press 1, speak → translates to English
[[hotkey]]
name = "translate"
key = "RightCommand"
channel = "1"
transform = "~/.config/speechbutton/scripts/transform_claude.py ~/.config/speechbutton/prompts/translate_en.md"

# Channel 2: hold Right Command, press 2, speak → append to notes
[[hotkey]]
name = "notes"
key = "RightCommand"
channel = "2"
file = "~/Documents/voice-notes.txt"
```

## Voice Activity Detection (Hands-Free)

```toml
[vad]
enabled = false                 # Toggle hands-free mode
chunk_silence_sec = 0.55        # Silence duration to end a speech segment
```

## Push-to-Talk Chunking

Split long recordings at silence gaps while you hold the button:

```toml
[ptt]
chunking_enabled = true         # Stream chunks during hold
chunk_silence_sec = 1.0         # Silence gap to trigger a chunk split
copy_to_clipboard = false       # Copy full text to clipboard after recording
```

## Auto-Send

Press Enter automatically after pasting (for chat apps):

```toml
auto_send = false
auto_send_hotkey = "ctrl+enter" # Hotkey to toggle auto-send on/off
send_delay_sec = 3.0            # Delay before pressing Enter
```

Hotkey format: `modifier+key` (e.g. `ctrl+enter`, `cmd+shift+space`, `alt+a`)

## JSON Output Format

When `output_format = "json"` or for webhooks:

```json
{
  "text": "Hello, this is a test.",
  "lang": "en",
  "model": "parakeet-tdt-0.6b-v3-int8",
  "duration_ms": 3200,
  "source": "ptt",
  "device": "MacBook Pro Microphone",
  "timestamp": "2026-04-01T12:00:00.000Z"
}
```

## Advanced Settings

```toml
[advanced]
audio_backend = "hal"           # "hal" (default) or "vpio" (noise suppression, mutes audio)
max_recording_sec = 300         # Safety limit
keep_audio_hot = false          # Keep mic stream always running (faster start)
```

## Device Rules

```toml
[[device_rule]]
match = "iPhone"                # Substring match (case-insensitive)
keep_hot = true                 # Keep audio stream always running for this device
```

## Voice Fingerprint

Drop voice segments that don't match the enrolled user's voice — works for both push-to-talk and hands-free. PTT recordings are split by silence into utterances and each is checked independently, so a foreign voice in a pause is dropped while your speech goes through.

```toml
[speaker_gate]
enabled = false                 # Master switch. Downloads ~25 MB WeSpeaker model on first enable.
threshold = 0.55                # Cosine-similarity cutoff (0.20–0.95). Higher = stricter rejection.
```

The first 5 recordings auto-enrol the user's voice. Per-microphone profiles (AirPods, MacBook built-in, USB headsets) are stored separately and live at `~/Library/Application Support/SpeechButton/models/speaker_profiles.json` — plain 256-dim vectors, no audio retained, never sent to the cloud.

See [Voice Fingerprint](voice-fingerprint.md) for the full guide, threshold tuning, and limits.

## Hallucination Filtering

Per-model word lists in `~/.config/speechbutton/hallucinations/`:
- `parakeet` — Parakeet TDT (few hallucinations)
- `cohere` — Cohere Transcribe (moderate)
- `whisper` — Whisper (most hallucinations, 7800+ phrases)

Format: one phrase per line. `lang:phrase` for language-specific, plain `phrase` for all languages.

## Local AI (Gemma 4)

Embedded LLM for text transforms:

```toml
[llm]
temperature = 0.3
top_k = 40
top_p = 0.9
context_size = 32768            # Up to 131072 (128K) for Gemma 4 E2B
max_tokens = 0                  # 0 = auto (fills remaining context)
```
