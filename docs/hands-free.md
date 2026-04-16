# Hands-Free Mode (VAD)

Hands-free mode uses Voice Activity Detection (VAD) to automatically detect when you speak. No button needed — just talk and text appears.

## Enable

Toggle in Settings, or in config:

```toml
[vad]
enabled = true
chunk_silence_sec = 0.55    # How long to wait before finalizing a segment
```

Or short-tap your PTT key (< 300ms) to toggle hands-free on/off.

## How It Works

1. SpeechButton listens continuously via Silero VAD (neural network, runs on CPU)
2. When speech is detected, the status dot turns from idle to pulsing
3. When you pause (silence > `chunk_silence_sec`), the segment is transcribed and pasted
4. If auto-send is on, Enter is pressed after `send_delay_sec`

## Auto-Send

Automatically presses Enter after pasting — useful for chat apps:

```toml
auto_send = true
send_delay_sec = 3.0        # Wait 3s of silence before pressing Enter
auto_send_hotkey = "ctrl+enter"   # Hotkey to toggle auto-send on/off
```

The delay is intentional: if you pause mid-thought, the next segment gets concatenated before Enter is pressed.

## Clipboard Backup

When `copy_to_clipboard = true` (under `[ptt]`), hands-free mode also copies transcriptions to the clipboard:

- While you keep speaking, chunks accumulate in the clipboard buffer
- If you pause longer than `send_delay_sec`, the next chunk starts a fresh buffer
- Cmd+V always gives you the most recent "thought"

## Status Indicator

The floating dot shows the current state:

| State | Appearance |
|-------|------------|
| Listening (idle) | Small steady dot |
| Speech detected | Pulsing dot (size tracks your voice level) |
| Processing | Brief blink |

The dot appears on all Desktops/Spaces as a heads-up overlay.

## Tips

- Set `chunk_silence_sec` lower (0.3-0.4) for faster response, higher (0.6-0.8) for fewer false splits
- Use `auto_send` with `send_delay_sec = 2.0` for fast chat, `4.0-5.0` for dictation with pauses
- The VAD model (Silero) runs on CPU and uses < 1% — no impact on battery
