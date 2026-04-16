# Shell Scripts Integration

SpeechButton can pipe transcribed text to any shell command via `exec`. This is the most flexible integration — anything you can do in a terminal, you can trigger with your voice.

## How It Works

```toml
exec = "your_command"
```

SpeechButton runs the command and writes the transcribed text to its **stdin**. The command's exit code is checked: if != 0, the output is cancelled.

## Examples

### Copy to clipboard (no paste)

```toml
exec = "pbcopy"
```

### Send email

```bash
#!/bin/bash
# ~/.config/speechbutton/scripts/send_email.sh
TEXT=$(cat)
echo "$TEXT" | mail -s "Voice note" you@example.com
```

```toml
exec = "~/.config/speechbutton/scripts/send_email.sh"
```

### Append to CSV with timestamp

```bash
#!/bin/bash
TEXT=$(cat)
echo "$(date -u +%Y-%m-%dT%H:%M:%SZ),\"$TEXT\"" >> ~/voice-log.csv
```

### HTTP POST with curl

```toml
exec = "curl -sS -X POST -d @- -H 'Content-Type: text/plain' http://localhost:8080/text"
```

### Notify via macOS notification

```bash
#!/bin/bash
TEXT=$(cat)
osascript -e "display notification \"$TEXT\" with title \"SpeechButton\""
```

### Pipe to another AI

```toml
# Send to Claude Code
exec = "claude -p --bare"

# Send to Ollama
exec = "ollama run llama3"

# Send to any OpenAI-compatible API
exec = "~/.config/speechbutton/scripts/openai_chat.sh"
```

## JSON mode

Set `output_format = "json"` and the exec command receives JSON instead of plain text:

```toml
[[hotkey]]
name = "json-pipe"
key = "RightCommand"
channel = "5"
exec = "python3 ~/process.py"
output_format = "json"
```

The script receives:
```json
{"text": "Hello world", "lang": "en", "model": "...", "duration_ms": 2100, ...}
```

## Tips

- Scripts must be executable: `chmod +x script.sh`
- Use `#!/usr/bin/env python3` for Python scripts
- Combine `transform` + `exec` for a two-stage pipeline: transform cleans the text, exec routes it
- If the exec command fails (exit code != 0), text is NOT pasted — useful as a validation gate
