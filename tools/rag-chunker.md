# RAG Chunker: split web content for vector databases

**Run it on Apify:** https://apify.com/ucchiii/rag-chunker · **Price:** $0.20 per 1,000 chunks · free demo with empty input

You crawled a website with `apify/rag-web-browser`, `apify/web-fetch` or `apify/website-content-crawler`. Now you need
**chunks** your vector database can embed. This Actor reads those datasets and writes **one row per chunk** — no
scraping, no LLM, no embedding API, no proxies.

- **Heading-aware splitting**: chunks follow Markdown headings and carry a `headingPath` (`["Guide", "Install"]`)
- **Overlap** between chunks to keep context; chunk size is set in characters
- **Code blocks are never cut in the middle** (an oversized block is split by lines and re-fenced)
- **Short fragments merged** into the previous chunk (`minChunkChars`)
- **Boilerplate removed**: repeated blank lines, image-only lines, link-only nav rows, short lines repeated on a page
- **Dedupe** identical chunks across pages and datasets
- Works for English and Japanese (sentence boundaries at `.`, `!`, `?`, `。`, `！`, `？`)
- Stable `chunkId` and `contentHash`, handy as vector IDs for upserts
- **$0.20 per 1,000 chunks written** ($0.0002 each). Reading, duplicates and merged fragments are free.

## Use cases
- Turn a crawled documentation site into chunks for a vector database (Pinecone, Qdrant, pgvector)
- Build a support chatbot knowledge base from help-center pages
- Re-chunk existing crawls with a different chunk size without crawling again
- Deduplicate repeated boilerplate across pages before embedding

## Input
```json
{
  "datasetIds": ["<dataset ID of your website-content-crawler run>"],
  "textField": "markdown",
  "chunkSize": 1500,
  "chunkOverlap": 200,
  "splitBy": "markdown-headings"
}
```
Leave `datasetIds` empty for a free demo on sample pages (rows are marked `_demo: true`).

Pages are read defensively: `url` / `metadata.url` / `crawl.loadedUrl`, `metadata.title` / `title`,
`metadata.languageCode`, and the body from `textField`, then `markdown`, then `text`.

## Output (one row per chunk)
```json
{
  "chunkId": "9f2c1a7b3d4e5f60", "url": "https://example.com/docs/getting-started", "title": "Getting started",
  "headingPath": ["Getting started", "Install"], "chunkIndex": 1, "chunkCount": 4,
  "text": "## Install\n\nInstall the package with pip ...", "charCount": 412, "approxTokens": 103,
  "languageCode": "en", "contentHash": "a1b2c3d4e5f60718", "sourceDatasetId": "abc123"
}
```
`approxTokens` is an **estimate (characters / 4)**, not a real tokenizer count. It is rough for Japanese and code.
Run statistics (`pagesRead`, `chunksCreated`, `duplicates`, `written`, ...) are saved in the key-value store as `STATS`.

## Use with LangChain, LlamaIndex, Pinecone
```python
from apify_client import ApifyClient
client = ApifyClient("<APIFY_TOKEN>")
run = client.actor("<this-actor>").call(run_input={"datasetIds": ["<crawler dataset>"]})
rows = list(client.dataset(run["defaultDatasetId"]).iterate_items())

# LangChain
from langchain_core.documents import Document
docs = [Document(page_content=r["text"], metadata={"source": r["url"], "title": r["title"], "headings": r["headingPath"]}) for r in rows]

# LlamaIndex
from llama_index.core import Document as LIDoc
nodes = [LIDoc(text=r["text"], id_=r["chunkId"], metadata={"url": r["url"], "title": r["title"]}) for r in rows]

# Pinecone (embed with your own model; chunkId is a stable vector ID)
index.upsert([(r["chunkId"], embed(r["text"]), {"url": r["url"], "text": r["text"]}) for r in rows])
```

## Notes
This Actor only processes datasets you already own; it does not access any website. You are responsible for how the
underlying content was collected and for your right to reuse it.

<!-- more-tools:start -->
## More tools from the same developer

- [HTML to Markdown Converter for LLM and RAG](https://apify.com/ucchiii/html-to-markdown) — clean Markdown from URLs, raw HTML or datasets for LLM and RAG
- [Sitemap URL Extractor](https://apify.com/ucchiii/sitemap-urls) — list every URL of a website from its sitemaps
- [Bulk URL Status Checker](https://apify.com/ucchiii/url-status-checker) — bulk HTTP status and redirect-chain checker for URL lists
- [RSS Feed Reader & Aggregator](https://apify.com/ucchiii/rss-feed-reader) — many RSS / Atom feeds merged into one clean article list
- [Dataset Merge & Dedup](https://apify.com/ucchiii/dataset-dedup-merge) — merge Apify datasets and remove duplicates by the fields you choose
<!-- more-tools:end -->

---
[All tools](../README.md) · Built and maintained by actor-factory on the Apify Store.
