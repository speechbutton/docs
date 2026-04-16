# Custom Webhooks

SpeechButton can POST transcriptions to any HTTP endpoint as JSON.

## Setup

```toml
[[hotkey]]
name = "api"
key = "RightCommand"
channel = "4"
webhook = "http://localhost:8080/voice"
```

## Payload

Every transcription sends a POST request with this JSON body:

```json
{
  "event": "transcription",
  "text": "Hello, this is a test.",
  "lang": "en",
  "model": "parakeet-tdt-0.6b-v3-int8",
  "duration_ms": 3200,
  "source": "ptt",
  "device": "MacBook Pro Microphone",
  "timestamp": "2026-04-01T12:00:00.000Z"
}
```

### Fields

| Field | Type | Description |
|-------|------|-------------|
| `event` | string | Always `"transcription"` |
| `text` | string | Full transcribed text (after transform if any) |
| `lang` | string | Detected language code |
| `model` | string | STT model used |
| `duration_ms` | number | Audio duration in milliseconds |
| `source` | string | `"ptt"` (push-to-talk) or `"vad"` (hands-free) |
| `device` | string | Input device name |
| `timestamp` | string | ISO 8601 UTC |

## With transform

Apply a transform before the webhook sends:

```toml
[[hotkey]]
name = "api-clean"
key = "RightCommand"
channel = "4"
transform = "~/.config/speechbutton/scripts/transform_claude.py ~/.config/speechbutton/prompts/cleanup.md"
webhook = "http://localhost:8080/voice"
```

## Example: receive with Python

```python
from flask import Flask, request
app = Flask(__name__)

@app.route('/voice', methods=['POST'])
def handle_voice():
    data = request.json
    print(f"[{data['lang']}] {data['text']}")
    return '', 200

app.run(port=8080)
```
