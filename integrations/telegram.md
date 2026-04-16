# Telegram Integration

Send voice-to-text messages directly to Telegram chats.

## Setup

### 1. Create a Telegram bot

1. Message [@BotFather](https://t.me/BotFather) on Telegram
2. `/newbot` → follow prompts → get your bot token
3. Send a message to your bot, then get your chat ID:
   ```bash
   curl -s "https://api.telegram.org/bot<TOKEN>/getUpdates" | python3 -c "import sys,json; print(json.load(sys.stdin)['result'][0]['message']['chat']['id'])"
   ```

### 2. Create the send script

Save as `~/.config/speechbutton/scripts/send_telegram.py`:

```python
#!/usr/bin/env python3
import sys, urllib.request, urllib.parse

BOT_TOKEN = "YOUR_BOT_TOKEN"
CHAT_ID = "YOUR_CHAT_ID"

text = sys.stdin.read().strip()
if not text:
    sys.exit(0)

url = f"https://api.telegram.org/bot{BOT_TOKEN}/sendMessage"
data = urllib.parse.urlencode({"chat_id": CHAT_ID, "text": text}).encode()
urllib.request.urlopen(url, data)
```

```bash
chmod +x ~/.config/speechbutton/scripts/send_telegram.py
```

### 3. Configure hotkey

```toml
[[hotkey]]
name = "telegram"
key = "RightCommand"
channel = "4"
exec = "~/.config/speechbutton/scripts/send_telegram.py"
```

## With translation

Speak in any language, auto-translate to English before sending:

```toml
[[hotkey]]
name = "telegram-en"
key = "RightCommand"
channel = "4"
transform = "~/.config/speechbutton/scripts/transform_claude.py ~/.config/speechbutton/prompts/translate_en.md"
exec = "~/.config/speechbutton/scripts/send_telegram.py"
```
