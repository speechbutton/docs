# Troubleshooting

## "Downloading Model" stuck or failed

- Check internet connection — first model download is ~600 MB
- Restart SpeechButton (menu bar → Quit, then relaunch)
- If behind a corporate proxy, the download may be blocked. Try from a personal network

## No text appears after speaking

1. **Check Accessibility permission:** System Settings → Privacy & Security → Accessibility → SpeechButton must be enabled
2. **Check the hotkey:** Default is Right Command. Try holding it and speaking. Check `config.toml` for the `key` setting
3. **Check the cursor:** Text is pasted where the cursor is. Click into a text field first

## Text appears but is wrong language

Set the language explicitly:
```toml
language = "en"    # or "ru", "de", etc.
```

`auto` works for most single-language scenarios, but if you mix languages, set it explicitly.

## Hallucinations (random text when not speaking)

SpeechButton has built-in hallucination filtering. If you see unwanted text:

1. Check that `hallucinationFilter` is enabled in Settings
2. The filter lists are in `~/.config/speechbutton/hallucinations/` — add new phrases if needed
3. For hands-free mode, an RMS-based gate automatically drops silent audio before it reaches the model

## Mic not detected

- Check `input_device` in config — set to `""` (empty) for system default
- SpeechButton requires microphone permission: System Settings → Privacy & Security → Microphone

## iPhone as microphone

Works out of the box via Continuity. Make sure:
- iPhone and Mac are on the same Apple ID
- Both have Bluetooth and Wi-Fi enabled
- Select "iPhone Microphone" in SpeechButton Settings

For best results with iPhone:
```toml
[[device_rule]]
match = "iPhone"
keep_hot = true
```

## Logs

Logs are at:
```
~/Library/Application Support/SpeechButton/logs/speechbutton.log
```

Include this file when reporting issues.
