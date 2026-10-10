---
name: repurpose-blog
description: >
  Repurpose long-form content into an SEO-friendly blog post. Use when the user
  wants to create a blog article from a video transcript, podcast, or other source
  material. Generates title variations, meta description, outline, and full draft
  with optional target keyword optimization.
---

# Blog Content Repurposer

Take any long-form content and produce a complete, SEO-optimized blog post that stands on its own, targets a keyword, and matches the creator's brand voice. Each run produces title variations, a meta description, a structured H2/H3 outline, and a full 800–1500 word draft — restructured from the source, not just reformatted — giving you a publish-ready blog article from a single piece of source material.

## Capabilities

- Accept a YouTube URL, blog/article URL, file path, or pasted text as the source
- Detect input type automatically and retrieve content without manual copy-paste
- Propose a target keyword from the source's key topics, or optimize around one the user provides
- Generate 3 title variations optimized for the target keyword
- Write a 150–160 character meta description with keyword included
- Produce a scannable H2/H3 outline before drafting
- Write an 800–1500 word draft restructured (not just reformatted) from the source
- Insert internal linking placeholders where relevant
- Apply brand voice if `brand-voice.md` is configured; fall back to neutral professional tone
- Save output to the configured destination folder with consistent naming
- Run first-run checks for config and brand voice before generating anything

## How to Use

Invoke this skill with any of the following:

- `Repurpose this into a blog post`
- `Create a blog article from [URL]`
- `Write a blog post from my transcript at [file path]`
- `Turn this podcast into a blog post`
- `/repurpose-blog`

## Input Parameters

| Parameter | Required | Description |
|-----------|----------|-------------|
| Source file or URL | Yes (or auto) | File path, YouTube URL, blog/article URL, or pasted text. If omitted, uses the most recent source in the configured folder. |
| Target keyword | No | The primary keyword to optimize the post around. If omitted, one will be inferred from the source and confirmed with you. |
| Brand voice | No | Automatically loaded from `brand-voice.md` if present. Override by passing voice notes directly in the message. |

## Output

A markdown file saved to the configured destination folder:

```
{source-title}-blog-{YYYY-MM-DD}.md
```

The file contains:
- 3 title variations optimized for the target keyword
- Meta description (150–160 chars) with keyword
- H2/H3 outline
- Full 800–1500 word draft with internal link placeholders
- A source reference linking back to the original content

---

## Phase 1: First-Run Checks

Before ingesting any content, run these checks in order.

### Check 1: repurposer-config.md

Look for the config file at:

```
~/.claude/plugins/repurposenow/repurposer-config.md
```

If the file does not exist, run the `setup-repurposer` skill inline before proceeding. Do not continue until the config is in place.

If the file exists, read it and note the configured destination folder for output delivery.

### Check 2: brand-voice.md

Look for the brand voice file at:

```
~/.claude/plugins/repurposenow/brand-voice.md
```

**If the file does not exist**, pause and offer setup:

> "Want to set up your brand voice first? It takes ~3 minutes and makes every output sound like you. Or skip for now and I'll use a neutral professional tone."

Wait for the user's response before continuing.

- If the user wants to set up brand voice: run the `setup-voice` skill inline, then return to Phase 2.
- If the user skips: proceed to Phase 2 with neutral professional tone. Add a reminder at the bottom of the output file:

  *"Tip: Run `/setup-voice` to make future outputs match your brand."*

**If the file exists**, load it now. It will govern tone, vocabulary, and style in the blog draft.

---

## Phase 2: Ingest Source Content

Reference `shared/ingestion.md` for the full ingestion flow. The steps below summarize what happens in this phase.

### Step 1: Detect Input Type

Inspect what the user provided:

| Input type | Indicators | Action |
|------------|-----------|--------|
| YouTube URL | `youtube.com/watch`, `youtu.be/` | Fetch transcript via Apify |
| Blog or web URL | Any other `http://` or `https://` URL | Scrape via Firecrawl |
| File path | Path starting with `/` or `~/`, or a local file reference | Read the file directly |
| No input | User has not provided a URL or file | Prompt the user for source content |

Check the user's message for a URL or file path before asking for anything. If one is present, proceed immediately.

### Step 2: Fetch Content

Retrieve the content using the appropriate method for the detected input type. On failure, fall back to requesting the text directly from the user. Full fallback handling is in `shared/ingestion.md`.

### Step 3: Parse into source-content.md

Extract the title, body text, key topics (3-5), and word count. For sources over 8,000 words, create a condensed 2,000-word version that preserves all data points, quotable moments, and key arguments. Write the normalized output following the `source-content.md` template in `shared/ingestion.md`.

### Step 4: Load Brand Voice

If `brand-voice.md` exists (confirmed in Phase 1), it is already loaded. Apply it in all writing phases below. If it does not exist, use neutral professional tone throughout.

---

## Phase 3: Keyword & SEO Setup

Before drafting, establish the target keyword that the blog post will be optimized around.

### Step 1: Determine the Target Keyword

**If the user provided a keyword** in their message, use it as the target keyword. Proceed to Step 2.

**If the user did not provide a keyword**, analyze the key topics extracted in Phase 2 and identify the best keyword candidate using this criteria:

| Criterion | What to evaluate |
|-----------|-----------------|
| Search intent fit | Does this keyword reflect something a reader would actively search for? |
| Specificity | Is it specific enough to be rankable, not so broad it's uncompetitive? |
| Alignment with source | Does it represent the core argument or insight of the source content? |
| Actionability | Would a post optimized around this keyword be genuinely useful to a searcher? |

Propose your top keyword candidate and explain the reasoning in one sentence. Ask the user to confirm or suggest an alternative:

> "Based on the source content, I'd suggest optimizing this post around: **[proposed keyword]**. Does that work, or would you prefer a different angle?"

Wait for confirmation before proceeding.

### Step 2: Confirm Keyword Placement Strategy

Once the target keyword is confirmed, plan its placement:

- **Title**: include naturally in at least 2 of the 3 title variations
- **Meta description**: include once
- **First H2 or opening paragraph**: include within the first 150 words of the draft
- **Body**: use naturally 2–4 times throughout the draft; do not force it
- **Avoid keyword stuffing**: if inserting the keyword reads awkwardly, use a semantic variation instead

---

## Phase 4: Generate Blog Draft

Using the confirmed keyword and normalized source content, produce the full blog output.

### Title Variations

Write 3 title options, each optimized for the target keyword. Vary the angle:

1. **Direct/informational** — states the value proposition clearly ("How to [X]", "The Complete Guide to [X]")
2. **Curiosity/contrarian** — creates intrigue or challenges expectations ("Why Most People Get [X] Wrong")
3. **List or framework** — signals structure and scannability ("[N] Ways to [X]", "The [X] Framework for [Y]")

Each title should:
- Include the target keyword naturally
- Be under 65 characters where possible (for SEO title display)
- Not be clickbait — the draft must deliver on the title's promise

### Meta Description

Write one meta description:
- 150–160 characters
- Includes the target keyword once
- Summarizes the post's value in plain language
- Ends with an implicit or explicit call to action where natural

### H2/H3 Outline

Before writing the full draft, produce the complete outline. Structure it for scannability:

- Use H2s for major sections (aim for 3–5)
- Use H3s for sub-points within sections where needed
- Each H2 should reflect a distinct idea, not just a chronological chunk of the source
- The outline should reflect the restructured argument of the post, not the sequence of the source

### Full Draft

Write the complete blog post following the outline. Rules:

- **Length**: 800–1500 words
- **Opening**: Do not start with "In this post, I will..." — open with a hook (a question, a bold claim, a specific scenario, or a striking data point)
- **Structure**: Use the H2/H3 outline as the backbone; include short paragraphs (2–4 sentences) and occasional single-sentence punches for emphasis
- **Restructured, not reformatted**: The draft must reorganize the source material into a new argument flow. Do not follow the source's original sequence. Surface the most important insight early, build context around it, then support with examples and data.
- **Internal link placeholders**: Where a subtopic could link to related content on the user's site, insert: `[INTERNAL LINK: related topic]`. Place 2–4 of these naturally throughout the draft.
- **Differentiation check**: Before finalizing, verify the post reads meaningfully differently from the source. If the source is a transcript, the blog should not read like a cleaned-up version of it — it should read like a purpose-written article.
- **Closing**: End with a clear takeaway and one forward-looking sentence or call to action.

If `brand-voice.md` is loaded, apply the tone, vocabulary register, structural tendencies, and any other guidelines from that profile throughout the entire draft.

### Draft Block Format

Use this structure for the output file:

```markdown
## Titles

1. [Title option 1]
2. [Title option 2]
3. [Title option 3]

---

## Meta Description

[150–160 char meta description]

---

## Outline

### [H2: Section 1]
- [H3 sub-point, if any]

### [H2: Section 2]
...

---

## Full Draft

[Complete 800–1500 word blog post]
```

---

## Phase 5: Deliver Output

### File Naming

Save the output file to the destination folder specified in `repurposer-config.md`:

```
{source-title}-blog-{YYYY-MM-DD}.md
```

- `source-title`: Slugified version of the source title (lowercase, hyphens, no special characters)
- `YYYY-MM-DD`: Today's date

### File Structure

```markdown
---
source_title: [title]
source_url: [URL or file path]
target_keyword: [confirmed keyword]
generated: [YYYY-MM-DD]
---

# Blog Post: [Source Title]

[Full blog output — see Phase 4 format]

---

## Source

[Title] — [URL or file path]
```

### Append Brand Voice Reminder (if applicable)

If the user skipped brand voice setup in Phase 1, append this line at the bottom of the file:

*"Tip: Run `/setup-voice` to make future outputs match your brand."*

---

## Phase 6: Post-First-Run Message

After delivering the output, check the `first_run_complete` flag in `repurposer-config.md`.

If `first_run_complete` is `false`, show this message:

> "Your blog post draft is ready. Here's what else you can do with the same source content:
>
> - `/repurpose-linkedin` — LinkedIn post variations
> - `/repurpose-twitter` — thread and individual tweet variations
> - `/repurpose-email` — newsletter section or standalone email
> - `/repurpose-carousel` — LinkedIn or Instagram carousel outline
> - `/repurpose-shorts` — TikTok and YouTube Shorts scripts
> - `/repurpose-all` — run all formats at once
>
> Run `/setup-voice` anytime to lock in your brand voice."

Then set `first_run_complete` to `true` in `repurposer-config.md`.

If `first_run_complete` is already `true`, skip this message.

---

## Quality Checklist

Before saving and delivering the output file, verify each item:

- [ ] Title includes target keyword naturally
- [ ] Meta description is under 160 chars
- [ ] H2/H3 structure is scannable
- [ ] Draft is 800–1500 words
- [ ] Content is restructured, not just reformatted from source
- [ ] Internal link placeholders are relevant
- [ ] Matches brand voice (if configured)
- [ ] Source reference appended
- [ ] Output file saved to configured destination with correct naming convention
- [ ] Brand voice reminder appended if setup was skipped
