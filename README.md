---
language:
  - zh
  - en
license: apache-2.0
tags:
  - text-to-speech
  - onnx
  - voice-cloning
  - diffusion
  - zero-shot
  - real-time
  - edge-deployment
  - audio
  - ConvNeXtV2
pipeline_tag: text-to-speech
library_name: custom
datasets:
  - seed-tts-eval
metrics:
  - word_error_rate
  - speaker_similarity
inference: false
---

# VoxFlash-TTS ⚡

**Ultra-Compressed Latent Diffusion for Real-Time Voice Cloning**

[![GitHub](https://img.shields.io/badge/GitHub-VoxFlashTTS-181717?logo=github)](https://github.com/VoxFlash/VoxFlashTTS)
[![Demo](https://img.shields.io/badge/Demo-voxflash.github.io-4a9eff)](https://voxflash.github.io/)
[![License](https://img.shields.io/badge/License-Apache%202.0-green)](LICENSE)
[![CUDA](https://img.shields.io/badge/CUDA-%3E%3D12.3.2-76b900?logo=nvidia)](https://developer.nvidia.com/cuda-toolkit)

VoxFlash-TTS is the fastest voice cloning system in the industry, supporting zero-shot cloning in both Chinese and English. Designed for low-latency, low-resource environments, it runs smoothly on consumer-grade GPUs and edge devices.

---

## Model Overview

The inference bottleneck in existing TTS systems is fundamentally a sequence length problem — high audio token density keeps computational cost prohibitively high. VoxFlash addresses this at the root by compressing the audio latent space to the extreme:

- **VAE** encodes 24kHz waveforms into a latent representation of only **9 frames/second (9 Hz)** — roughly 8× more compressed than EnCodec (75 fps) and 2.4× more than Stable Audio (21.5 fps)
- Generating 10 seconds of audio requires the diffusion model to process just **90 latent vectors**, rather than hundreds or thousands of tokens
- End-to-end compute is reduced by orders of magnitude compared to conventional approaches, enabling **millisecond-level inference**

### Architecture

| Module | Design | Role |
|---|---|---|
| VAE Encoder | Lightweight convolutional network | Waveform → 9 Hz latent vectors |
| Phoneme Encoder | ConvNeXtV2 | Text phoneme feature extraction |
| Alignment | Coarse-grained explicit alignment | Phoneme sequence → latent sequence mapping |
| Diffusion Model | Multi-step iterative denoising (NFE=16) | Speech generation in latent space |
| VAE Decoder | Lightweight convolutional network | Latent vectors → high-fidelity waveform |
| Speaker Encoder | Reference audio embedding | Zero-shot voice injection |

---

## Quick Start

### Requirements

- CUDA >= 12.3.2
- Docker

### Docker Deployment

```bash
# Pull the image
docker pull berlinisaiah/ttsv2:v1

# Run in background (production)
docker container run -d --gpus all \
  --mount type=bind,source=$(pwd)/resources,target=/app/resources \
  -p 8000:8000 berlinisaiah/ttsv2:v1
```

Once running, open `http://127.0.0.1:8000/demo.html` to access the WebUI.

### Debug Mode

```bash
docker container run -it --gpus all \
  --mount type=bind,source=$(pwd)/resources,target=/app/resources \
  -p 8000:8000 berlinisaiah/ttsv2:v1
```

---

## Capabilities

### Supported Languages

| Language | Same-language Cloning | Cross-lingual Cloning |
|---|---|---|
| Chinese (Mandarin) | ✅ | ✅ |
| English | ✅ | ✅ |

### Zero-Shot Voice Cloning

No fine-tuning required for target speakers. Provide a reference audio clip and the model extracts a speaker embedding, injects it into the generation process, and outputs speech that closely matches the target voice.

Cross-lingual cloning is also supported (e.g. Chinese reference audio → English output), demonstrating effective **disentanglement of voice timbre from language identity**.

### Inference Speed

Thanks to the extreme 9 Hz temporal compression, VoxFlash achieves millisecond-level inference on consumer GPUs — significantly faster than comparable systems — making it well-suited for latency-sensitive real-time applications.

---

## Evaluation

Evaluation samples are drawn from the [Seed-TTS](https://arxiv.org/abs/2406.02430) benchmark, enabling direct comparison with leading industry systems.

| System | Inference Speed | Deployment Cost | Zero-Shot Cloning | Cross-lingual |
|---|---|---|---|---|
| Seed-TTS | Slow | High (cloud) | ✅ | ✅ |
| CosyVoice 2 | Medium | Medium | ✅ | ✅ |
| FastSpeech variants | Fast | Low | ❌ | ❌ |
| **VoxFlash-TTS** | **Fastest** | **Low (edge)** | **✅** | **✅** |

> For audio samples and detailed comparisons, visit the [Demo page](https://voxflash.github.io/).

---

## Use Cases

- **Real-time voice interaction** — Extremely low first-packet latency; ideal for dialogue systems and live dubbing
- **Large-scale batch synthesis** — Significantly lower compute cost than comparable systems; suited for audiobooks and content platforms
- **Edge / on-device deployment** — Low VRAM footprint; runs on consumer-grade GPUs
- **Individual developers** — One-command Docker deployment with no complex tuning required

---

## Model Size

| File | Size | Contents |
|---|---|---|
| `main_model.onnx` | 697 MB | Phoneme Encoder + Diffusion Model + Speaker Encoder |
| `vae_decode.onnx` | 51.5 MB | VAE Decoder |
| `vae_encode.onnx` | 46.1 MB | VAE Encoder |
| `vocoder.onnx` | 59.7 MB | Vocoder |
| **Total** | **~854 MB** | Full pipeline |

> Exact parameter count is not yet available. File sizes reflect the exported ONNX models used for inference.

---

## Limitations

- Audio quality under extreme compression ratios may fall short of quality-focused systems such as Seed-TTS
- The current version is optimized primarily for Chinese and English; performance on other languages has not been systematically evaluated
- Accent naturalness in cross-lingual cloning has room for improvement
- Very short reference audio (< 3 seconds) may reduce speaker similarity

---

## Citation

If VoxFlash-TTS has been useful in your research or engineering work, please cite:

```bibtex
@misc{voxflash2026,
  title     = {VoxFlash-TTS: Ultra-Compressed Latent Diffusion for Real-Time Voice Cloning},
  author    = {VoxFlash},
  year      = {2026},
  url       = {https://github.com/VoxFlash/VoxFlashTTS},
  note      = {GitHub repository}
}
```

---

## Contact

- 📧 Email: [zhangtaiyan072@gmail.com](mailto:zhangtaiyan072@gmail.com)
- 🐙 GitHub: [github.com/VoxFlash/VoxFlashTTS](https://github.com/VoxFlash/VoxFlashTTS)
- 🌐 Demo: [voxflash.github.io](https://voxflash.github.io/)

---

*VoxFlash-TTS — Not the most expressive TTS, but the fastest, lightest, and easiest-to-deploy voice cloning system.*
