# Slack Integration

Dictate messages directly to Slack channels using SpeechButton's `exec` output.

## Setup

### 1. Create a Slack webhook

Go to [Slack API](https://api.slack.com/messaging/webhooks) and create an Incoming Webhook for your workspace. Copy the webhook URL.

### 2. Create the send script

Save as `~/.config/speechbutton/scripts/send_slack.py`:

```python
#!/usr/bin/env python3
import sys, json, urllib.request

WEBHOOK_URL = "https://hooks.slack.com/services/YOUR/WEBHOOK/URL"

text = sys.stdin.read().strip()
if not text:
    sys.exit(0)

data = json.dumps({"text": text}).encode()
req = urllib.request.Request(WEBHOOK_URL, data=data, headers={"Content-Type": "application/json"})
urllib.request.urlopen(req)
```

```bash
chmod +x ~/.config/speechbutton/scripts/send_slack.py
```

### 3. Configure hotkey

```toml
[[hotkey]]
name = "slack"
key = "RightCommand"
channel = "3"
exec = "~/.config/speechbutton/scripts/send_slack.py"
```

Now hold Right Command + press 3, speak, release — message appears in Slack.

## With cleanup transform

```toml
[[hotkey]]
name = "slack"
key = "RightCommand"
channel = "3"
transform = "~/.config/speechbutton/scripts/transform_claude.py ~/.config/speechbutton/prompts/cleanup.md"
exec = "~/.config/speechbutton/scripts/send_slack.py"
```

This removes filler words and fixes grammar before sending to Slack.
