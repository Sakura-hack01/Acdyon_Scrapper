# Acdyon Technologies Ingestion Service

A high-performance data ingestion pipeline developed for **Track 1** of the Acdyon Technologies Engineering Assessment.

This service is engineered to reliably extract structured data from web sources by bypassing common network-layer bot detection mechanisms (WAFs, Cloudflare, DataDome), while avoiding the CPU and memory overhead typically associated with headless browser automation.

## Architecture & Features

- **Network-Layer Fingerprint Fidelity** — Utilizes `curl_cffi` to replicate the TLS (JA3/JA4) and HTTP/2 fingerprints of a standard Chrome browser, ensuring requests are indistinguishable from organic traffic at the transport layer.
- **Resilient Extraction** — Parsing logic is fully decoupled from the network fetcher, enabling graceful degradation in the event of upstream schema changes. Transient HTTP failures are handled via exponential backoff using `tenacity`.
- **Humanized Request Pacing** — Introduces randomized jitter between requests to avoid time-series clustering and reduce the likelihood of behavioral flagging.
- **Containerized Deployment** — Fully Dockerized to ensure deterministic builds and production-ready deployment.

> **Note to Reviewers:** For a detailed discussion of the detection surface, ingestion strategy, and the design considerations for evolving this synchronous prototype into a distributed, Kafka-driven streaming system, please refer to [`DECISIONS.md`](./DECISIONS.md).

## Tech Stack

| Layer | Technology |
|---|---|
| Language | Python 3.10 |
| API Framework | FastAPI |
| Network & Fingerprinting | `curl_cffi` |
| HTML Parsing | `beautifulsoup4` |
| Retry Logic | `tenacity` |
| Infrastructure | Docker |

## Getting Started

### Option 1: Run via Docker (Recommended)

1. Build the image:
   ```bash
   docker build -t acdyon-scraper .
   ```

2. Run the container:
   ```bash
   docker run -p 8000:8000 acdyon-scraper
   ```

3. Trigger the pipeline by navigating to:
   ```
   http://127.0.0.1:8000/scrape
   ```

### Option 2: Run via Local Python Environment

1. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```

2. Start the server:
   ```bash
   uvicorn main:app --host 127.0.0.1 --port 8000
   ```

3. Trigger the pipeline by navigating to:
   ```
   http://127.0.0.1:8000/scrape
   ```

## API Reference

### `GET /scrape`

Executes the ingestion pipeline against a low-risk demonstration target (Hacker News Jobs), showcasing end-to-end extraction capability in a manner consistent with the target's Terms of Service.

**Response:**

```json
{
  "status": "success",
  "data": [
    {
      "title": "Company Name Is Hiring a Software Engineer",
      "link": "https://news.ycombinator.com/item?id=XXXXXXX",
      "source": "Hacker News Jobs"
    }
  ]
}
```

## Project Structure

```
.
├── main.py            # FastAPI application entry point
├── requirements.txt    # Python dependencies
├── Dockerfile          # Container build definition
├── DECISIONS.md        # Design rationale and scaling strategy
└── README.md
```
## You can check out the webiste (https://acdyon-scrapper.onrender.com/scrape)

## Disclaimer

This project is intended solely for educational and assessment purposes. The demonstration endpoint targets a publicly accessible, low-risk data source (Hacker News Jobs) and is designed to operate within that source's Terms of Service. Deploying the underlying stealth techniques against third-party services should only be done with appropriate authorization and in compliance with applicable laws and terms of service.
