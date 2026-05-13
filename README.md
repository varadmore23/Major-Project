# AIR Main Audio Bulletins Scraper

This scraper pulls main audio bulletins from the News On Air archive using the official
admin-ajax endpoints and parses the HTML with BeautifulSoup. It downloads audio files,
optionally transcribes them, and appends metadata to a CSV.

## Setup

1) Create and activate a Python virtual environment.
2) Install dependencies:

```bash
pip install -r requirements.txt
```

## List Categories

```bash
python air_bulletins_scraper.py --language english --list-categories
```

Example slugs you will see:
- hourly-news
- evening-news
- midday-news
- morning-news
- hourly-news-hi

## Download Hourly News for a Month

```bash
python air_bulletins_scraper.py --language english --category hourly-news --month 2026-05
```

Or with explicit dates:

```bash
python air_bulletins_scraper.py --language english --category hourly-news --from 2026-05-01 --to 2026-05-31
```

Outputs:
- Audio files: output/audio/YYYY-MM-DD/
- Transcripts (optional): output/text/YYYY-MM-DD/
- CSV metadata: output/metadata.csv

## Transcription (Optional)

The script supports two local transcribers:
- faster-whisper (recommended for speed)
- openai-whisper (reference implementation)

Example:

```bash
python air_bulletins_scraper.py --language english --category hourly-news --month 2026-05 \
  --transcribe --transcriber faster-whisper --model base
```

Install a transcriber and ffmpeg:

```bash
pip install faster-whisper
```

For openai-whisper:

```bash
pip install openai-whisper
```

## De-duplication

Use --dedupe to remove repeated lead-in content across consecutive hours.

```bash
python air_bulletins_scraper.py --language english --category hourly-news --month 2026-05 \
  --transcribe --dedupe
```

## Politeness Controls

You can slow requests to reduce load:

```bash
python air_bulletins_scraper.py --language english --category hourly-news --month 2026-05 \
  --min-delay 1.5 --max-delay 4.0 --max-retries 3
```

## Running the AIR News Intelligence System (Phase 1)

After scraping and transcribing the audio bulletins, you can run the full RAG pipeline and chatbot.

### 1. Ingest Transcripts

Run the ingestion pipeline to process all new transcripts, classify genres, filter duplicates, generate embeddings, and build the FAISS index and SQLite metadata database.

```bash
python ingest_pipeline.py
```

To wipe the existing index and re-process all transcripts from scratch:

```bash
python ingest_pipeline.py --reindex
```

### 2. Start the Chatbot API and Web UI

Start the FastAPI server which exposes the chat endpoints and serves the Web UI.

```bash
uvicorn chatbot_api:app --reload --port 8000
```

### 3. Access the Web UI

Open your browser and navigate to:
[http://localhost:8000](http://localhost:8000)

### (Optional) Setup Gemini LLM for Better Answers

For generated answers, export your Google Gemini API key. If not set, the chatbot will fall back to extractive answers (just listing the relevant sources).

**Windows (PowerShell):**
```powershell
$env:GEMINI_API_KEY="your_api_key_here"
```

**Linux/macOS:**
```bash
export GEMINI_API_KEY="your_api_key_here"
```

### Check Vector Store Stats

You can view the statistics of the stored segments, date ranges, and genre breakdowns:

```bash
python vector_store.py --stats
```
