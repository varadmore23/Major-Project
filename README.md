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
