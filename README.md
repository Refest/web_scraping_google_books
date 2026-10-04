# Google Books Scraping and Text Processing

An educational Python project that collects book metadata using the Google Books REST API and HTML scraping, then cleans and analyzes book descriptions in English and Indonesian.

## Authors

**Group 2**

- Muhammad Refi Setyawan — student ID suffix: 159
- Doni Wahana Putra — student ID suffix: 248

## What this project does

| Notebook | Source | Collection workflow |
| --- | --- | --- |
| `01_google_books_api.ipynb` | Google Books API v1 | Searches for a book, inspects a JSON response, and requests up to 10 matching volumes. |
| `02_google_books_http.ipynb` | Google Books website | Requests one book page and extracts metadata using BeautifulSoup, with fallback selectors for the description. |

Both notebooks create a pandas DataFrame and apply the same text-processing pipeline. The API notebook uses the example query `Laskar Pelangi Andrea Hirata`. The HTTP notebook uses volume ID `ZPi96HIG76AC`.

The project processes metadata and descriptions; it does not download complete book contents.

## Repository structure

Suggested GitHub repository name: **`google-books-scraping-text-processing`**.

Keep both notebooks in the repository root. This matches their current use of `Path.cwd()` for output directories.

| Repository path | Purpose |
| --- | --- |
| `README.md` | Project overview, API setup, and execution instructions. |
| `requirements.txt` | Python dependencies. |
| `.gitignore` | Excludes local environments, notebook checkpoints, NLTK downloads, and secret files. |
| `01_google_books_api.ipynb` | API collection and text processing. |
| `02_google_books_http.ipynb` | HTML collection and text processing. |
| `hasil_text_processing_api/google_books_api_preprocessed.csv` | Included API dataset with preprocessing stages. |
| `hasil_text_processing_api/frekuensi_kata.csv` | Included word-frequency table. |
| `hasil_text_processing_api/wordcloud.png` | Included before/after stopword-removal visualization. |
| `hasil_text_processing_http/google_books_http_preprocessed.csv` | Generated when the HTTP notebook runs successfully. |
| `hasil_text_processing_http/frekuensi_kata.csv` | Generated HTTP word frequencies. |
| `hasil_text_processing_http/wordcloud.png` | Generated if the HTTP description produces usable tokens. |

The HTTP output files are not included in this initial package. Their directory is created by the notebook. Output folder names and `frekuensi_kata.csv` are retained to match the existing code.

### File naming

Use lowercase names and underscores for notebook and data filenames. Avoid spaces, download-copy suffixes such as `(1)`, and working labels such as `AA` or `BB`.

| Original filename | Repository filename |
| --- | --- |
| `Scraping_API_Google_Books_159_248_Text_ProcessingBB (1).ipynb` | `01_google_books_api.ipynb` |
| `Scraping_HTTP_Google_Books_159_248_Text_ProcessingAA.ipynb` | `02_google_books_http.ipynb` |
| `google_books_api_preprocessed.csv` | `hasil_text_processing_api/google_books_api_preprocessed.csv` |
| `frekuensi_kata.csv` | `hasil_text_processing_api/frekuensi_kata.csv` |
| `wordcloud.png` | `hasil_text_processing_api/wordcloud.png` |

## Installation

Use Python 3.10 or newer as a recommended starting point. Dependencies are not version-pinned; this package does not claim a tested, frozen environment.

From the repository root, create a virtual environment:

```bash
python -m venv .venv
```

Activate it using the command for your platform:

| Platform | Command |
| --- | --- |
| Windows PowerShell | `.venv\Scripts\Activate.ps1` |
| Windows Command Prompt | `.venv\Scripts\activate.bat` |
| macOS / Linux | `source .venv/bin/activate` |

Install dependencies and start Jupyter:

```bash
python -m pip install -r requirements.txt
python -m jupyterlab
```

In VS Code, select the same virtual environment as the notebook kernel. Internet access is needed for Google requests and the first NLTK resource downloads.

## How to get a Google Books API key

This project accesses public metadata, so an API key is sufficient; an OAuth consent screen is unnecessary for this workflow. See [Google Books authentication guidance](https://developers.google.com/books/docs/v1/using#auth).

1. Sign in to [Google Cloud Console](https://console.cloud.google.com/).
2. Use the project selector to create or select a project, for example `google-books-text-processing`.
3. Open **APIs & Services → Library**, search for **Books API**, and enable it. [Open the Books API page](https://console.cloud.google.com/apis/library/books.googleapis.com).
4. In the same project, open **APIs & Services → Credentials → Create credentials → API key**.
5. Name the key and restrict it to **Books API**. Depending on the console layout, restrictions may appear during creation or on the key's edit page. Save the settings and copy the key privately.
6. For Python notebook requests, use IP restrictions when you have a stable public IP. Website restrictions do not match this client. For temporary local testing, application restrictions can remain unset while the Books API restriction stays enabled.

See [Google's API key management guide](https://cloud.google.com/docs/authentication/api-keys) for current console instructions. Check your project's quota page instead of assuming a fixed daily request limit.

### Configure the API notebook

The supplied notebook contains this placeholder:

```python
api_key = 'GOOGLE BOOK API KEY'
```

For a local session, replace that line with a hidden prompt:

```python
import os
from getpass import getpass

api_key = os.environ.get('GOOGLE_BOOKS_API_KEY') or getpass('Google Books API key: ')
```

Leave `query`, `api_url`, and the request code in place. Enter the key when prompted, or set `GOOGLE_BOOKS_API_KEY` in the environment before launching Jupyter. This change is a setup instruction; the packaged notebook still contains its original placeholder.

No `.env` file is loaded automatically by the current notebooks. Creating one alone will not configure the key.

### Check the key

After configuring `api_key`, this optional cell makes a small test request:

```python
import requests

response = requests.get(
    'https://www.googleapis.com/books/v1/volumes',
    params={'q': 'Laskar Pelangi Andrea Hirata', 'maxResults': 1, 'key': api_key},
    timeout=30,
)
payload = response.json()

print('Status code:', response.status_code)
if response.status_code != 200:
    raise RuntimeError(payload.get('error', {}).get('message', 'Request failed'))
if not payload.get('items'):
    raise ValueError('Request succeeded, but no matching books were returned.')
print('Title:', payload['items'][0].get('volumeInfo', {}).get('title'))
```

Do not print the key or the full request URL. Do not commit a real key in notebook source, outputs, or screenshots. If a key has already been published, rotate it in Google Cloud.

## Running the notebooks

1. Launch Jupyter from the repository root and confirm the notebook's working directory with `Path.cwd()` if necessary.
2. Open `01_google_books_api.ipynb`, configure the key, and run cells from top to bottom.
3. Inspect the API response and DataFrame before running the final text-processing cell.
4. Open `02_google_books_http.ipynb` and run its cells independently. This notebook does not use an API key.
5. Inspect the generated CSV files and word clouds in each method's output directory.

Rerunning a notebook overwrites its output files in that working directory. Results may differ from the included snapshot because upstream metadata and HTML can change.

## Text-processing pipeline

1. Preserve the original description and flag empty descriptions.
2. Remove HTML and normalize Unicode.
3. Remove hashtags, convert to lowercase, and remove URLs and email addresses.
4. Remove emojis, common emoticons, punctuation, symbols, and numbers.
5. Normalize spaces and split text into tokens.
6. Choose English or Indonesian using stopword-match counts, with book-language metadata as a fallback when available.
7. Remove language-specific stopwords while retaining selected negation words.
8. Apply Porter stemming for English or Sastrawi stemming for Indonesian.
9. Produce a separate POS-aware English WordNet lemmatization column.
10. Export preprocessing stages, token counts, word frequencies, and a word-cloud comparison.

`prepro` uses the stemming output. `lemma_en` is a separate English-only result. Frequent-word and rare-word removal columns are demonstrations and do not replace `prepro`.

Frequency analysis and word clouds use unique nonempty `clean_text` descriptions to avoid counting duplicate descriptions repeatedly. Frequencies are calculated after stopword removal and before stemming. The language-selection method is a heuristic, not a trained language detector.

## Included results

The supplied API snapshot contains **10 records**: **9 descriptions available** and **1 empty description**. The nonempty descriptions are classified as **4 English** and **5 Indonesian**. The frequency CSV contains **924 language/word entries**.

![API descriptions before and after stopword removal](hasil_text_processing_api/wordcloud.png)

### Important output columns

Column names remain in the original notebook language.

| Column | Meaning |
| --- | --- |
| `judul`, `deskripsi` | Book title and original description. |
| `penulis`, `kategori`, `terbit` | Authors, categories, and publication date, where collected by the API. |
| `bahasa_buku`, `bahasa_deskripsi` | Book-language metadata and selected description language. |
| `status_processing` | Whether a description is available for processing. |
| `clean_text` | Text after cleaning and whitespace normalization. |
| `tokens`, `tokens_no_stopwords` | Token lists, exported as JSON strings in CSV. |
| `stem`, `lemma_en`, `prepro` | Stemmed text, English lemmas, and the selected final text. |
| `token_sebelum`, `token_setelah` | Whitespace-based token counts before and after processing. |
| `bahasa`, `kata`, `frekuensi` | Language, word, and count in the frequency table. |

## Troubleshooting

| Problem | What to check |
| --- | --- |
| Invalid API key | Configure the real key at runtime and remove the placeholder. |
| API disabled or HTTP 403 | Check the response message, enabled Books API, project, key restrictions, and quota. |
| HTTP 429 or quota error | Inspect project quotas and retry later with fewer requests. |
| No matching books | Change the search query and check whether `items` exists. |
| Missing description or rating | Metadata is optional; an empty field is not automatically a scraping failure. |
| NLTK `LookupError` | Allow the final cell to download its resources into `.nltk_data`, or the configured `NLTK_DATA` directory. |
| HTML description not found | Inspect the downloaded page; its structure may have changed or it may be a consent/block page. |
| Files saved elsewhere | Check `Path.cwd()`; output directories are relative to it. |

## GitHub commit

Create an empty repository named `google-books-scraping-text-processing`, extract this package, and open a terminal in the extracted project directory. Replace `YOUR_USERNAME` before running:

```bash
git init
git add README.md requirements.txt .gitignore 01_google_books_api.ipynb 02_google_books_http.ipynb hasil_text_processing_api/
git diff --cached --stat
git commit -m "Add Google Books scraping and text processing notebooks"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/google-books-scraping-text-processing.git
git push -u origin main
```

Before committing, review notebook source and saved outputs for credentials. If you choose to clear outputs, rerun only with runtime key entry before retaining new outputs. An ignored secret file does not remove a key embedded in a tracked notebook.

## References

- [Google Books API documentation](https://developers.google.com/books/docs/v1/using)
- [Google Books API volume search reference](https://developers.google.com/books/docs/v1/reference/volumes/list)
- [Google Cloud API key management](https://cloud.google.com/docs/authentication/api-keys)

Book descriptions and metadata come from Google Books and their respective providers. This repository is an educational project; no redistribution license for third-party content is implied.
