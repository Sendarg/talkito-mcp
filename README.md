# TalkiTo - Voice-Enabled AI Development

<div align="center">

[![GitHub Stars](https://img.shields.io/github/stars/Sendarg/talkito-mcp?style=social)](https://github.com/Sendarg/talkito-mcp/stargazers)
[![GitHub Forks](https://img.shields.io/github/forks/Sendarg/talkito-mcp?style=social)](https://github.com/Sendarg/talkito-mcp/network/members)
[![License](https://img.shields.io/badge/License-AGPL%20v3-blue.svg?style=flat-square)](https://github.com/Sendarg/talkito-mcp/blob/main/LICENSE)
[![Python Version](https://img.shields.io/badge/python-3.10%2B-blue?style=flat-square)](https://www.python.org/downloads/)

**Transform your AI coding experience with voice interaction**

> 🔀 **Forked from [robdmac/talkito](https://github.com/robdmac/talkito)**

[Quick Start](#-quick-start) • [Features](#-key-features) • [Configuration](#-configuration) • [Demo](#-demo)

</div>

---

## 🎯 What is TalkiTo?

TalkiTo brings natural speech capabilities to AI development tools. Talk to Claude Code, OpenAI Codex, and other AI assistants using your voice, and receive spoken responses in real-time.

**Use Cases:**
- 🗣️ **Hands-free coding** - Code while away from keyboard
- 🔊 **Accessibility** - Make AI tools accessible to everyone
- 📱 **Remote monitoring** - Get notifications via WhatsApp/Slack/SMS
- 🎙️ **Voice-first workflows** - Natural conversation with AI assistants

---

## ✨ Key Features

- **Start-Instant TTS** - Blocking queue & immediate processing reduces initial latency to near zero
- **Zero-Gap Pipeline** - Parallel synthesis allows gapless playback of sequential sentences
- **Reliable Cloud TTS** - Smart retry logic handles transient network failures (e.g. 503 errors)
- **Start Up Faster** - 90% faster startup (launches in ~2 seconds) with lazy-loading optimization
- **Full context reading** - Reads complete agent responses from session history logs
- **Smart TTS fallback** - Automatic fallback to Kokoro TTS ensures uninterrupted speech
- **Audio caching** - Intelligent caching reduces latency and API costs
- **TTS Translation** - Auto-translate non-English text to English for TTS (via LLM API)
- **Multiple providers** - OpenAI, AWS Polly, Azure, Google Cloud, ElevenLabs, Deepgram, Kokoro, KittenTTS
- **MCP Server support** - Run as standalone Model Context Protocol server
- **Global configuration** - Settings persist in `.env` or `.talkito.env` files

---

## 🚀 Quick Start

### One-Line Installation

```bash
curl -sSL https://raw.githubusercontent.com/Sendarg/talkito-mcp/main/install.sh | bash
```

### Or Install via PyPI

```bash
pip install talkito
```

### Launch with Claude Code

```bash
talkito claude
```

---

## 📦 Installation

### From Source

```bash
# Clone repository
git clone https://github.com/Sendarg/talkito-mcp.git
cd talkito-mcp

# Create virtual environment
python3 -m venv venv
source venv/bin/activate

# Install system dependencies (macOS)
brew install portaudio

# Install TalkiTo
pip install .  # or pip install -e . for development

# Run
talkito claude
```

### System Dependencies

**macOS:**
```bash
brew install portaudio
```

**Ubuntu/Debian:**
```bash
sudo apt-get install portaudio19-dev python3-pyaudio espeak
```

---

## 🎮 Usage

### Basic Commands

```bash
# Launch with Claude Code
talkito claude

# Launch with Codex CLI
talkito codex

# Run as MCP server
talkito --mcp-server --port=8000

# Run configuration menu
talkito
```

### TTS Configuration

```bash
# Use different TTS providers
talkito --tts-provider openai --tts-voice nova claude
talkito --tts-provider kokoro --tts-voice af_heart claude
talkito --tts-provider azure --tts-voice en-US-JennyNeural claude

# Disable auto-skip
talkito --dont-auto-skip-tts claude
```

### ASR Configuration

```bash
# Use different ASR providers
talkito --asr-provider gcloud --asr-language en-US claude
talkito --asr-provider local_whisper claude

# Configure Whisper
WHISPER_MODEL=small WHISPER_COMPUTE_TYPE=int8 talkito --asr-provider local_whisper claude
```

---

## 🔧 Configuration

### Environment Files

```bash
# Copy example configuration
cp .env.example .env

# Edit with your API keys
nano .env
```

TalkiTo supports `.env` (primary) and `.talkito.env` (secondary) files.

### Global Configuration

TalkiTo looks for configuration files in the following order:
1. `.env` in current working directory
2. `.talkito.env` in current working directory
3. `~/.env` in home directory
4. `~/.talkito.env` in home directory

See `.env.example` for all available configuration options.

---

## 🎙️ Provider Setup

### TTS Providers (Quick Reference)

| Provider | Setup | Cost | Quality |
|----------|-------|------|---------|
| **System** | No setup | Free | Basic |
| **Kokoro** | `pip install 'kokoro>=0.9.4' soundfile phonemizer` | Free (local) | High |
| **OpenAI** | `export OPENAI_API_KEY="sk-..."` | $0.015/1K chars | High |
| **AWS Polly** | AWS credentials | $4/1M chars | High |
| **Azure** | `export AZURE_SPEECH_KEY="..."` | $15/1M chars | Very High |
| **Google Cloud** | Service account JSON | $16/1M chars | Very High |

#### Recommended: Kokoro (Local, Privacy-First)

```bash
pip install 'kokoro>=0.9.4' soundfile phonemizer
talkito --tts-provider kokoro --tts-voice af_heart claude
```
- No API key required
- Runs completely offline
- High quality multilingual voices

#### OpenAI TTS

```bash
export OPENAI_API_KEY="sk-..."
talkito --tts-provider openai --tts-voice nova claude
```
- Voices: alloy, echo, fable, onyx, nova, shimmer
- Get API Key: https://platform.openai.com/api-keys

### ASR Providers (Quick Reference)

| Provider | Setup | Cost | Quality |
|----------|-------|------|---------|
| **Google** | No setup | Free | Good |
| **Local Whisper** | `pip install faster-whisper` | Free (local) | Excellent |
| **Google Cloud** | Service account JSON | $0.006/15s | Excellent |
| **Azure** | `export AZURE_SPEECH_KEY="..."` | $1/hour | Excellent |

#### Recommended: Local Whisper (Privacy-First)

```bash
pip install faster-whisper
WHISPER_MODEL=small WHISPER_COMPUTE_TYPE=int8 talkito --asr-provider local_whisper claude
```
- No API key required
- Runs completely offline
- Excellent accuracy

### TTS Translation (Auto-Translate for Speech)

When TTS is configured for English but the AI responds in another language (e.g., Chinese), TalkiTo can auto-translate the text before speaking.

```bash
# Configure translation (uses OpenAI-compatible API)
export LLM_API_KEY="your-api-key"
export LLM_BASE_URL="https://api.openai.com/v1"  # Optional, defaults to OpenAI
export LLM_MODEL="gpt-4o-mini"  # Optional

# Run TalkiTo - translation happens automatically when needed
```

**How it works:**
- Detects text language using Unicode character analysis
- If text language ≠ TTS voice language, translates via LLM
- Simplifies long English text into conversational language
- If translation fails, TTS is skipped (no garbled output)

---

## 🤖 AI Assistant Compatibility

| AI Assistant | Method | Status | Voice I/O |
|--------------|--------|--------|-----------|
| **Claude Code** | Terminal | ✅ Fully Supported | ✅ / ✅ |
| **Codex CLI** | Terminal | ✅ Fully Supported | ✅ / ✅ |
| bolt.new | Web Extension | ⚠️ Output Only | ❌ / ✅ |
| v0.dev | Web Extension | ⚠️ Output Only | ❌ / ✅ |

---

## 🆕 What's New in v0.3.1

### Performance
- **Start-Instant TTS** - Replaced polling with blocking queue for immediate processing
- **Zero-Gap Pipeline** - Synthesizes next sentence while current is playing
- **90% faster startup** - Reduced from 20+ seconds to ~2 seconds
- **Lazy loading** - TTS/ASR providers load only when needed
- **Audio caching** - Intelligent caching reduces API calls

### Reliability
- **Smart Retry Logic** - Google TTS automatically retries on network failures (503, Socket Closed)
- **Sequential Playback** - Fixed auto-skip issues to ensure sentences play in order without interruption
- **Full context reading** - Reads complete agent responses from session history logs
- **Smart TTS fallback** - Automatic fallback to Kokoro TTS
- **Robust error handling** - Better recovery from provider failures

### Features
- **MCP Server support** - Run as standalone Model Context Protocol server
- **Global configuration** - Settings persist in `.env` or `.talkito.env` files
- **Per-agent voice config** - Different voices for different AI assistants

---

## 📚 Standalone Module Usage

### TTS Module

```python
import tts

engine = tts.detect_tts_engine()
tts.start_tts_worker(engine)
tts.queue_for_speech("Hello from TTS!")

import time
time.sleep(2)
tts.shutdown_tts()
```

### ASR Module

```python
import asr

def handle_text(text):
    print(f"You said: {text}")

asr.start_dictation(handle_text)
```

---

## 🎬 Demo

[![TalkiTo Demo](https://img.youtube.com/vi/dliBN7pNAuw/0.jpg)](https://youtu.be/dliBN7pNAuw)

---

## 📄 License

GNU Affero General Public License v3.0 or later - see [LICENSE](LICENSE)

**Copyright (C) 2025 Robert Macrae**

---

## 🔗 Links

- **Homepage**: https://github.com/Sendarg/talkito-mcp
- **Original Project**: https://github.com/robdmac/talkito
- **Issues**: https://github.com/Sendarg/talkito-mcp/issues
- **PyPI**: https://pypi.org/project/talkito/
