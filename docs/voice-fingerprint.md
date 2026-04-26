# Voice Fingerprint

Voice Fingerprint filters out speech that doesn't match the enrolled user's voice. It runs **before** transcription, so segments from other speakers never reach the model and never appear in your output.

It works in both push-to-talk and hands-free mode. PTT recordings are split by silence into utterances and each is checked independently — a foreign voice in a pause is dropped while your speech goes through.

> Looking for the announcement and the deeper *why*? Read the blog post: [Only your voice — Voice Fingerprint in SpeechButton 2.12](https://www.speechbutton.com/blog/voice-fingerprint.html).

---

## Enable

Open **Settings → Voice Fingerprint** and toggle **"Only respond to my voice"** on.

The first time you enable the feature, SpeechButton downloads a one-shot **~25 MB** model (WeSpeaker ResNet-34, Apache-2.0). After that, the feature works fully offline — no further network access from the gate.

In `config.toml`:

```toml
[speaker_gate]
enabled = true        # Master switch. Downloads ~25 MB WeSpeaker model on first enable.
threshold = 0.55      # Cosine-similarity cutoff (0.20–0.95). Higher = stricter rejection.
```

See [Configuration → Voice Fingerprint](configuration.md#voice-fingerprint) for the full TOML reference.

---

## Calibration

There is no "training mode". The first **5** recordings you make after enabling the feature are auto-enrolled into your profile, regardless of what you say.

The Settings panel shows progress:

```
Profile:   Not enrolled yet
Profile:   2/5 enrolling…
Profile:   Enrolled (5 recordings)
```

From the sixth recording onwards, the gate is active. Every accepted segment after that nudges the profile by **5 %** (exponential moving average), so it adapts to a head cold, a new microphone batch, or gradual changes in your voice — without one bad recording dragging the profile off course.

Want to start over? **Settings → Voice Fingerprint → Reset voice profile** wipes the file and begins fresh.

---

## How it works

| Step | What happens |
|------|--------------|
| 1 | Speech segment arrives (≥1 s at 16 kHz mono) |
| 2 | WeSpeaker ResNet-34 produces a 256-dim L2-normalised embedding (~5–10 ms on Apple Silicon) |
| 3 | Cosine similarity is computed against the active per-device profile |
| 4 | `≥ threshold` → accept + update profile · `< threshold` → drop, segment never reaches ASR |

Same speaker on clean audio typically scores **0.65 – 0.85**. Different speakers land between **0.10 – 0.40**. The default threshold of **0.55** is conservative — it errs on the side of accepting the user.

---

## Per-device profiles

The same human voice produces meaningfully different embeddings on AirPods vs MacBook built-in mic vs USB headset — Bluetooth codecs, near/far-field acoustics, and band-pass filtering all distort the spectral shape WeSpeaker keys on.

SpeechButton stores **one profile per input device**, keyed by the macOS device name. Switching from MacBook mic to AirPods restarts the 5-recording bootstrap on the AirPods side. The MacBook profile stays intact and is reused next time you plug in.

You don't have to do anything. The Profile counter in Settings will show `1/5 enrolling…` after switching to a new device, then return to `Enrolled` once the new device is calibrated.

---

## Threshold tuning

Default: **0.55**. Adjustable in Settings (slider range 0.30 – 0.85, step 0.05) or directly in `config.toml`.

| Symptom | Suggested change |
|---------|------------------|
| Your own voice is sometimes rejected (especially after a cold or on a noisy day) | Lower threshold by 0.05 (e.g. 0.55 → 0.50) |
| Voices from people near you sometimes leak through | Raise threshold by 0.05 (e.g. 0.55 → 0.60) |
| Working in a very loud shared space | 0.65 – 0.70 with a re-enrolment for each microphone you use there |
| Working alone, threshold is causing friction | 0.45 – 0.50 |

The Rust core clamps to **0.20 – 0.95** if you set values outside the UI range manually. Below ~0.40 the gate accepts almost anything; above ~0.85 you'll start rejecting your own voice on noisy days.

---

## Storage and privacy

Profiles are stored as plain JSON at:

```
~/Library/Application Support/SpeechButton/models/speaker_profiles.json
```

The file is small — about **1 KB per device profile**. It contains only the averaged 256-dim embedding plus a counter. No raw audio is ever stored.

Voice Fingerprint is **fully on-device**:

- The WeSpeaker model runs locally inside SpeechButton.
- Your fingerprint never leaves your Mac. There is no upload step.
- The gate code path opens no network sockets. The only network call related to this feature is the one-time model download on first enable.
- Disabling the feature stops the gate immediately. Resetting the profile wipes the file.

You can read, back up, or delete `speaker_profiles.json` at any time.

---

## FAQ / limits

**Will it ever trigger on someone else's voice?**
Rarely, but yes. At the default 0.55 threshold a small percentage of foreign segments will pass — particularly close family members on the same microphone in the same room. Raise the threshold if this is an issue for your environment.

**Will it ever reject me?**
Sometimes, especially with a head cold, a brand-new microphone, or a noisy environment. The bootstrap (first 5 recordings) is permissive specifically so this doesn't lock new users out. After bootstrap, the EMA absorbs gradual changes; if your voice changes a lot suddenly, lower the threshold or reset the profile.

**Identical twins?**
Voice Fingerprint will not reliably tell identical twins apart. This is a known limitation of speaker verification in general, not a SpeechButton-specific shortcoming.

**Does it work in languages other than English?**
WeSpeaker was trained on English speech (VoxCeleb). It still produces useful fingerprints in other languages — voice timbre is largely language-independent — but expect slightly noisier scores. Lower the threshold by 0.05 if you notice more false drops.

**Does it support Intel Macs?**
SpeechButton 2.12 supports Apple Silicon (M1, M2, M3, M4) only. Voice Fingerprint inherits that requirement.

**What happens when I switch microphones?**
A fresh per-device profile starts — five normal uses on the new device and you're recognised again. The other device's profile is untouched.

**Can I export or import my profile to another Mac?**
Yes — copy `speaker_profiles.json` to the same path on the other Mac. Keep in mind the per-device keying: profiles only match if the macOS device name strings line up.

---

## Related

- [Configuration → Voice Fingerprint](configuration.md#voice-fingerprint) — TOML reference for `[speaker_gate]`.
- [Hands-Free Mode](hands-free.md) — VAD chunking, where each chunk is checked independently against your fingerprint.
- [Changelog](../changelog.md#v2120) — release notes for SpeechButton 2.12.
- [Blog: Only your voice](https://www.speechbutton.com/blog/voice-fingerprint.html) — narrative announcement and use-case walk-through.

---

Voice fingerprinting in SpeechButton uses the open-source [WeSpeaker](https://github.com/wenet-e2e/wespeaker) model (`wespeaker_en_voxceleb_resnet34.onnx`) and the [sherpa-onnx](https://github.com/k2-fsa/sherpa-onnx) runtime, both released under Apache-2.0.
