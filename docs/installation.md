# Installation

## System Requirements

- macOS 14 Sonoma or later
- Apple Silicon (M1, M2, M3, M4)

## Option 1: Homebrew (recommended)

```bash
brew install --cask speechbutton/tap/speechbutton
```

Auto-updates are built into the app — it checks for new versions on launch and downloads them automatically.

## Option 2: Direct Download

Download the latest DMG from the [releases page](https://github.com/speechbutton/speechbutton-dist/releases/latest).

1. Open the DMG
2. Drag SpeechButton to Applications
3. Launch from Applications (or Spotlight: `Cmd+Space` → "SpeechButton")
4. Grant Accessibility permission when prompted (needed for pasting text)

## First Launch

On first launch, SpeechButton will:

1. Download the default STT model (~600 MB, one-time)
2. Create the config directory at `~/.config/speechbutton/`
3. Set the default hotkey to **Right Command** (hold to record)

You can change everything in Settings or by editing `~/.config/speechbutton/config.toml`.

## Uninstall

**Homebrew:**
```bash
brew uninstall --cask speechbutton
```

**Manual:**
1. Quit SpeechButton (menu bar → Quit)
2. Delete `/Applications/SpeechButton.app`
3. Optionally remove config and data:
   ```bash
   rm -rf ~/.config/speechbutton
   rm -rf ~/Library/Application\ Support/SpeechButton
   ```
