# 🎥 AI Video Assistant

An AI-powered tool that takes a **YouTube URL or local video/audio file** and automatically transcribes, summarizes, extracts key insights, and lets you **chat with your meeting** using RAG (Retrieval-Augmented Generation).

---

## 🚀 What It Does

1. **Downloads** audio from a YouTube URL (or accepts a local file)
2. **Transcribes** the audio using OpenAI Whisper (English) or Sarvam AI (Hinglish)
3. **Summarizes** the transcript using Mistral AI
4. **Extracts** action items, key decisions, and open questions
5. **Lets you chat** with the transcript via a RAG pipeline (ChromaDB + LangChain + Mistral)

---

## 🗂️ Project Structure

```
VIDEO ASSISTANT/
├── utils/
│   └── audio_process.py      # Downloads YouTube audio, converts to WAV, chunks it
├── core/
│   ├── transcriber.py        # Whisper (English) + Sarvam AI (Hinglish) transcription
│   ├── summarize.py          # Summarization + title generation via Mistral AI
│   ├── extractor.py          # Extracts action items, decisions, and questions
│   ├── vector_store.py       # Builds and loads ChromaDB vector store
│   └── rag_engine.py         # RAG pipeline for Q&A over the transcript
├── main.py                   # CLI entry point — runs the full pipeline
├── .env                      # API keys (not committed to Git)
├── .gitignore
└── requirements.txt
```

---

## ⚙️ Tech Stack

| Tool | Purpose |
|---|---|
| `yt-dlp` | Download audio from YouTube |
| `pydub` | Audio conversion and chunking |
| `openai-whisper` | Local speech-to-text (English) |
| `Sarvam AI API` | Speech-to-text with translation (Hinglish → English) |
| `LangChain` | LLM pipeline and RAG orchestration |
| `Mistral AI` | Summarization, extraction, and Q&A |
| `ChromaDB` | Vector database for RAG |
| `sentence-transformers` | Embeddings (`all-MiniLM-L6-v2`) |
| `ffmpeg` | Audio processing backend |
| `streamlit` | (UI - coming soon) |

---

## 🛠️ Setup & Installation

### Prerequisites

- Python 3.13+
- [uv](https://github.com/astral-sh/uv) package manager
- [ffmpeg](https://ffmpeg.org/) installed on your system

### 1. Clone the repo

```bash
git clone https://github.com/abhinav-ghosh/AI-Video-Assistant.git
cd AI-Video-Assistant
```

### 2. Create virtual environment

```bash
uv venv
```

Activate it:

- **Windows (PowerShell):**
  ```powershell
  Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
  .venv\Scripts\activate
  ```
- **Mac/Linux:**
  ```bash
  source .venv/bin/activate
  ```

### 3. Install dependencies

```bash
uv pip install -r requirements.txt
```

> **Python 3.13+ users:** `audioop` was removed from the standard library. Install the backport:
> ```bash
> uv pip install audioop-lts
> ```

### 4. Install ffmpeg

**Windows:**
```powershell
winget install --id Gyan.FFmpeg
```
Then restart your terminal.

**Mac:**
```bash
brew install ffmpeg
```

**Linux:**
```bash
sudo apt install ffmpeg
```

### 5. Set up API keys

Create a `.env` file in the root directory:

```env
MISTRAL_API_KEY=your_mistral_api_key_here
SARVAM_API_KEY=your_sarvam_api_key_here
```

- Get Mistral API key: https://console.mistral.ai/
- Get Sarvam API key: https://www.sarvam.ai/

---

## ▶️ Running the App

```bash
python main.py
```

You'll be prompted for:

```
Enter YouTube URL or local file path: https://youtu.be/your_video
Language (english/hinglish): english
```

### Output

```
📌 Title: <generated meeting title>

📋 Summary:
<bullet point summary>

✅ Action Items:
<numbered list of tasks with owners and deadlines>

🔑 Key Decisions:
<numbered list of decisions made>

❓ Open Questions:
<numbered list of unresolved questions>

💬 Chat with your meeting (type 'exit' to quit)
You: What was decided about the budget?
🤖 Assistant: <answer from the transcript>
```

---

## 🧠 How It Works

### Pipeline Overview

```
YouTube URL / Local File
        ↓
  audio_process.py
  (download + chunk into 10-min WAV segments)
        ↓
  transcriber.py
  (Whisper for English / Sarvam AI for Hinglish)
        ↓
  Full Transcript
        ↓
  ┌─────────────────────────────────┐
  │  summarize.py  → Summary        │
  │  extractor.py  → Action Items   │
  │                → Decisions      │
  │                → Questions      │
  │  rag_engine.py → Vector Store   │
  │                → Q&A Chat       │
  └─────────────────────────────────┘
```

### Language Support

| Language | Engine | Notes |
|---|---|---|
| English | OpenAI Whisper (local) | Runs fully offline, no API needed |
| Hinglish | Sarvam AI (API) | Translates to English while transcribing |

### RAG (Chat with your meeting)

The transcript is split into chunks, embedded using `all-MiniLM-L6-v2`, and stored in ChromaDB. When you ask a question, the most relevant chunks are retrieved and passed to Mistral AI to generate a grounded answer.

---

## 📝 Notes

- Whisper runs on CPU by default. GPU support requires CUDA.
- Large videos may take time to transcribe locally with Whisper.
- The `downloades/` folder stores downloaded audio files (excluded from Git).
- The `vector_db/` folder stores the ChromaDB index (excluded from Git).

---

## 📄 License

MIT License
