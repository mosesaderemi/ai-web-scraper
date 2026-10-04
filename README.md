# AI Web Scraper

A Python tool that crawls a business directory and uses a large language model to turn each page into structured records. Results are saved to a CSV file. The default configuration collects dentists in Toronto from Yellow Pages Canada, and it can be pointed at any similar listing site.

## How It Works

1. Crawl4AI opens each result page in a headless Chromium browser.
2. A CSS selector narrows the page to the listing elements, so only the relevant HTML is sent to the model.
3. An LLM, called through LiteLLM, extracts the name, address, phone number, website and a one-sentence description for each business, validated against a Pydantic model.
4. Duplicate names are skipped, records from all pages are combined, and the result is written to `businesses_data.csv`.
5. The crawl stops at the page limit or when a page returns no results, with a pause between requests.

## Features

- LLM-based extraction that does not depend on brittle per-field selectors
- Provider-agnostic: change `LLM_MODEL` to use Gemini, OpenAI, Groq or any model supported by LiteLLM
- Typed output through a Pydantic schema
- Duplicate filtering and polite request pacing
- Token usage report at the end of the run

## Tech Stack

- Python 3.10 or later
- Crawl4AI 0.4.247
- Pydantic 2
- python-dotenv

## Project Structure

```text
.
├── main.py              Crawl loop and CSV export
├── config.py            Target URL, CSS selector, page limit, model, instructions
├── models/business.py   BusinessData schema
├── src
│   ├── scraper.py       Browser config, LLM strategy, page processing
│   └── utils.py         Duplicate check and CSV writer
├── sample_output.csv    Example of the output format
└── requirements.txt
```

## Getting Started

```bash
git clone https://github.com/mosesaderemi/ai-web-scraper.git
cd ai-web-scraper
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
crawl4ai-setup
```

Create a `.env` file with the key for the model provider you use:

```bash
GEMINI_API_KEY=your_key
```

Run the scraper:

```bash
python main.py
```

The output is written to `businesses_data.csv` in the project folder.

## Configuration

Edit `config.py`:

| Setting | Purpose |
| --- | --- |
| `LLM_MODEL` | Model identifier, for example `gemini/gemini-2.0-flash` |
| `BASE_URL` | Listing URL with a `{page_number}` placeholder |
| `CSS_SELECTOR` | Element that wraps each listing |
| `MAX_PAGES` | Maximum number of pages to crawl |
| `SCRAPER_INSTRUCTIONS` | Prompt describing the fields to extract |

To scrape a different category or city, change `BASE_URL`, for example to plumbers in Vancouver, and adjust the selector if the site layout differs.

## Responsible Use

Check the terms of service and `robots.txt` of any website before scraping it, keep request rates low, and handle collected contact details in line with applicable data protection rules.

## Author

Aderemi Moses Timileyin
GitHub: [@mosesaderemi](https://github.com/mosesaderemi)
LinkedIn: [linkedin.com/in/mosesaderemi-6a70a5405](https://www.linkedin.com/in/mosesaderemi-6a70a5405)

## License

Released under the MIT License. See the `LICENSE` file for details.
