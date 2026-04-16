# STT Models

SpeechButton supports multiple speech-to-text models. Choose based on your language needs and speed preference.

## Available Models

| Model | ID | Languages | Punctuation | Speed | Size |
|-------|-----|-----------|-------------|-------|------|
| **Parakeet V3** | `parakeet-tdt-0.6b-v3-int8` | 25 | Built-in | Fastest | ~600 MB |
| **Qwen3** | `qwen3` | 30+ | Built-in | Fast | ~1.2 GB |
| **Cohere Transcribe** | `cohere-transcribe-int8` | 14 | Built-in | Fast | ~400 MB |
| **Whisper Turbo** | `ggml-large-v3-turbo-q5_0.bin` | 100+ | Built-in | Moderate | ~570 MB |

## Recommendations

- **English only:** Parakeet V3 — fastest, most accurate for English
- **English + European:** Parakeet V3 (supports 25 languages including EN, RU, DE, FR, ES, etc.)
- **Asian languages (Japanese, Chinese, Korean, Vietnamese):** Qwen3
- **Rare languages (100+):** Whisper Turbo
- **Balanced:** Cohere Transcribe — good across 14 major languages

## Changing Models

In `config.toml`:
```toml
model = "parakeet-tdt-0.6b-v3-int8"
```

Or use the model picker in the Settings menu. First switch downloads the model (~30-60s on a fast connection).

## All Models Run Locally

Every model runs 100% on your Mac — no data leaves your machine. Models use the Apple Neural Engine (ANE) or GPU via CoreML/Metal for maximum speed.
