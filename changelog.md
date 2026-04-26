# Changelog

Release notes for SpeechButton. Most recent first. Full diffs are in the [GitHub releases](https://github.com/speechbutton/speechbutton-dist/releases).

---

## v2.12.1 — Voice Fingerprint hot-fix <a id="v2121"></a>

> **2026-04-26 · Patch**

- **Live enrollment counter.** The Settings panel now updates the *Profile: N/5 enrolling…* state immediately after each accepted recording, instead of staying on *Not enrolled yet* until the app was relaunched.
- **Speaker gate auto-init at startup.** With Voice Fingerprint enabled, the gate is now active on app launch. Previously it only initialised when `config.toml` was edited externally.

---

## v2.12.0 — Voice Fingerprint <a id="v2120"></a>

> **2026-04-26 · Privacy & compatibility release**

- **Voice Fingerprint.** SpeechButton now transcribes only the speaker who set it up. Background voices, family in the next room, audio from speakers — filtered out before reaching the model. Per-microphone profiles, 100 % on device. See the [Voice Fingerprint guide](docs/voice-fingerprint.md) and the [announcement post](https://www.speechbutton.com/blog/voice-fingerprint.html).
- **macOS 14 Sonoma compatibility.** App previously failed to launch on Sonoma due to a Metal API mismatch — fixed.
- **Notch pill alignment fix.** A grey gap in the recording indicator on Sonoma laptops is gone.
- **PTT silence-chunk fix.** Indicator no longer pulses when you hold the key in silence.

---

## v2.11.0 — Audio polish & CJK <a id="v2110"></a>

- **Mute system audio while recording.** YouTube, Spotify, Zoom and other apps are quietly ducked while a recording is in progress, then restored. No more transcripts polluted by whatever was playing in the background.
- **Cohere model warm-up.** The Cohere transcribe model now warms when selected, so the first recording after a model switch no longer pays cold-start latency.
- **Asian-language ASR improvements.** Better recognition for Japanese, Chinese, Korean, and Cantonese.

---

## v2.10.0 — Hallucination overhaul <a id="v2100"></a>

- **Per-model hallucination filtering.** Whisper, Parakeet, and Cohere each have their own characteristic filler outputs. SpeechButton ships a tuned word-list per model so phrases like *"thanks for watching"* don't end up in your text. See [`hallucinations/`](https://github.com/speechbutton/config/tree/main/hallucinations) in the config repo.
- **Hands-free clipboard mode.** VAD chunks now land on the clipboard instead of being pasted into whatever window has focus. Speak, then paste where you actually want the text. See [Hands-Free Mode](docs/hands-free.md).
- **Model cleanup utility.** Remove unused STT models from the local cache from inside Settings.

---

## v2.9.0 — Toast redesign <a id="v290"></a>

- **Notch-aware pill toast.** Recording indicator wraps around the MacBook notch and feels native to it, with a floating pulse during recording.
- **Configurable indicator position.** `dot_position` in `config.toml` lets you place the indicator anywhere along the screen edge.
- **Proportional styling on external displays.** Indicator scales correctly on high-DPI external monitors.

---

## v2.8.1 and earlier

Earlier release notes live in the [GitHub releases page](https://github.com/speechbutton/speechbutton-dist/releases).
