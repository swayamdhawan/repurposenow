# Shared Ingestion Module

> This is a shared reference module, not a standalone skill. Every repurposenow format skill includes this module in its Phase 1. Follow these steps exactly before beginning any content transformation.

---

## Phase 1: Ingest Source Content

### Step 1a: Detect Input Type

Inspect what the user provided:

| Input type | Indicators | Action |
|------------|-----------|--------|
| **YouTube URL** | `youtube.com/watch`, `youtu.be/` | Fetch transcript via Apify (Step 1b) |
| **Blog or web URL** | Any other `http://` or `https://` URL | Scrape via Firecrawl (Step 1c) |
| **File path** | Path starting with `/` or `~/`, or a local file reference | Read the file directly (Step 1d) |
| **No input** | User has not provided a URL or file | Prompt the user (Step 1e) |

Check the user's message for a URL or file path before asking for anything. If one is present, proceed immediately.

---

### Step 1b: YouTube — Fetch Transcript via Apify

When the source is a YouTube URL, use the Apify YouTube Transcript Scraper:

```
mcp__apify__topaz_sharingan-slash-Youtube-Transcript-Scraper-1(
  startUrls: ["[youtube_url]"],
  timestamps: false
)
```

This returns the full spoken transcript of the video as plain text.

**If the transcript scraper fails or returns empty results:**

- Verify the URL is a valid, public YouTube video
- Try appending the full watch URL format: `https://www.youtube.com/watch?v=[VIDEO_ID]`
- If still unavailable, fall back to Firecrawl to scrape the YouTube page for title, description, and any auto-generated caption text:

```
mcp__firecrawl__firecrawl_scrape(
  url: "[youtube_url]"
)
```

- Note in `source-content.md` that transcript was unavailable and content is based on metadata/description only.

**If Apify is unavailable entirely:**

Tell the user: "Apify is currently unavailable. I can't pull the YouTube transcript automatically. Please paste the transcript text directly into this conversation and I'll proceed from there."

---

### Step 1c: Blog, Podcast, or Web Page — Scrape via Firecrawl

When the source is a non-YouTube URL, use Firecrawl to extract the full article or page content:

```
mcp__firecrawl__firecrawl_scrape(
  url: "[blog_url]"
)
```

This returns the page content as clean markdown, stripping navigation, ads, and boilerplate.

**If the page requires JavaScript rendering or returns minimal content:**

The Firecrawl scrape result will be sparse. In that case, note in `source-content.md` that only partial content was available.

**If Firecrawl is unavailable entirely:**

Tell the user: "Firecrawl is currently unavailable. I can't scrape the page automatically. Please paste the article or podcast transcript text directly into this conversation and I'll proceed from there."

---

### Step 1d: File — Read Directly

When the user provides a file path (e.g., a transcript, document, or export):

- Read the file from the provided path
- Treat the file content as the raw body text
- Infer source type from the file extension or content:
  - `.txt`, `.md`: treat as a transcript or article body
  - `.pdf`: extract all readable text
  - Any other format: use available content and note the file type in `source-content.md`

---

### Step 1e: No Input Provided

If the user has not provided a URL or file, ask:

"What's the source content you'd like to repurpose? Please share a YouTube link, a blog/article URL, or paste the text directly."

Do not proceed to parsing until a source is provided.

---

## Phase 2: Parse Content

Once raw content is retrieved (from transcript, scrape, or file), extract the following:

### Title

- For YouTube: use the video title from the Apify result or the page `<title>` tag
- For blog/web: use the article headline (typically the first H1 or the page title)
- For files: use the filename (without extension) as a fallback, or the first line if it reads as a title
- If no title is detectable, use `[Untitled — [source_type]]`

### Body Text

- Strip all navigation, sidebars, footers, ads, comment sections, and unrelated boilerplate
- Keep: the main article or transcript text, pull quotes, subheadings, and numbered/bulleted lists that are part of the content
- For transcripts: keep the spoken content as-is; light cleanup is fine (remove filler like "[inaudible]" or repeated timestamps if timestamps are off)

### Key Topics

Identify 3-5 main themes or subjects covered in the content. These are the ideas the content primarily explores, not every subtopic mentioned. Look for:

- The central argument or thesis
- Major supporting points
- Specific subjects that receive sustained attention (more than a paragraph or 30+ seconds)

### Word Count

Estimate the word count of the normalized body text. This determines whether condensation is required in Step 3.

---

## Phase 3: Handle Long Sources (8,000+ Words)

If the normalized body text exceeds approximately 8,000 words, create a condensed version alongside the full content.

### Condensation Strategy

Write a 2,000-word summary that:

1. **Preserves the core argument and conclusion** — the reader should understand the source's main point after reading the condensed version alone
2. **Retains all data points, statistics, and cited sources** — numbers and evidence are the most repurposable elements; never drop them
3. **Captures quotable moments** — direct quotes, memorable phrasing, and strong single sentences that could stand alone as pull quotes or social copy
4. **Maintains the original order** — follow the source's structure so format skills can reference specific sections
5. **Does not editorialize** — the condensed version is a faithful compression, not a reinterpretation

Do not use bullet summaries alone. Write in flowing prose that reflects the original tone.

---

## Phase 4: Load Brand Voice

Before writing any output, check for a brand voice file.

### Check for brand-voice.md

Look for the file at:

```
~/.claude/plugins/repurposenow/brand-voice.md
```

**If the file exists:** Read it fully. Apply all guidelines from it when writing format outputs. The brand voice file overrides any default stylistic choices.

**If the file does not exist:** Proceed without it. Use a neutral, professional tone that matches the source content's register. Do not mention to the user that the brand voice file is missing unless they ask why the output sounds a particular way.

**Brand voice scope:** The brand voice file governs tone, vocabulary, and style in the final repurposed outputs. It does not affect the `source-content.md` normalized output, which is always a faithful representation of the source.

---

## Phase 5: Write source-content.md

After parsing, write the normalized output to `source-content.md`. This file is what format skills read to generate their outputs. It must follow this exact template:

```markdown
---
title: [Extracted title]
source_type: [youtube | blog | podcast | file]
source_url: [URL if applicable, or file path]
word_count: [approximate]
ingested: [YYYY-MM-DD]
---

## Key Topics
1. [topic]
2. [topic]
3. [topic]

## Full Content
[normalized body text]

## Condensed Version (if over 8,000 words)
[2,000-word summary preserving key insights, data points, and quotable moments]
```

### Field notes

- `source_type`: Use `youtube` for YouTube videos, `blog` for articles and web pages, `podcast` for audio transcripts, `file` for local file input
- `source_url`: Include the full URL for web sources; include the file path for local files; leave blank only if neither applies
- `word_count`: Round to the nearest 100 (e.g., `~3,200`)
- `ingested`: Use today's date in YYYY-MM-DD format
- The `## Condensed Version` section is omitted entirely if word count is under 8,000 words
- Key Topics: list 3-5 items; use sentence fragments (not full sentences); be specific rather than generic (e.g., "Impact of async work on team trust" not "Work culture")

---

## Ingestion Checklist

Before handing off to a format skill, confirm:

- [ ] Input type was correctly identified (YouTube / blog / file / pasted text)
- [ ] Raw content was successfully retrieved (transcript, scraped text, or file contents)
- [ ] Title, body, key topics, and word count were extracted
- [ ] Body text is clean (boilerplate removed, no navigation fragments)
- [ ] `source-content.md` was written using the exact template above
- [ ] Condensed version is included if word count exceeds 8,000 words
- [ ] Brand voice file was checked; findings noted for use in format outputs
- [ ] Any retrieval failures were noted in `source-content.md` with explanation
