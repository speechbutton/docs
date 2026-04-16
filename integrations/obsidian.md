# Obsidian Integration

Append voice notes directly to your Obsidian vault using SpeechButton's `file` output.

## Simple Setup — Append to daily note

```toml
[[hotkey]]
name = "obsidian"
key = "RightCommand"
channel = "2"
file = "~/Documents/Obsidian/MyVault/Voice Notes.md"
```

Hold Right Command + press 2, speak, release — transcription is appended as a new line.

## With timestamp

Use `exec` to format with a timestamp:

```toml
[[hotkey]]
name = "obsidian-ts"
key = "RightCommand"
channel = "2"
exec = "~/.config/speechbutton/scripts/obsidian_append.sh"
```

Script `~/.config/speechbutton/scripts/obsidian_append.sh`:

```bash
#!/bin/bash
VAULT="$HOME/Documents/Obsidian/MyVault"
FILE="$VAULT/Voice Notes.md"
TEXT=$(cat)
TIMESTAMP=$(date "+%Y-%m-%d %H:%M")
echo "" >> "$FILE"
echo "- **$TIMESTAMP** — $TEXT" >> "$FILE"
```

## With daily note (auto-create today's file)

```bash
#!/bin/bash
VAULT="$HOME/Documents/Obsidian/MyVault/Daily"
TODAY=$(date "+%Y-%m-%d")
FILE="$VAULT/$TODAY.md"

if [ ! -f "$FILE" ]; then
    echo "# $TODAY" > "$FILE"
    echo "" >> "$FILE"
fi

TEXT=$(cat)
TIMESTAMP=$(date "+%H:%M")
echo "- **$TIMESTAMP** — $TEXT" >> "$FILE"
```

## Tips

- Obsidian watches for file changes — notes appear instantly
- Use `transform` to clean up filler words before saving
- For JSON format: set `output_format = "json"` to get structured data with timestamps and language
