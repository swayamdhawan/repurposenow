---
name: repurpose-twitter
description: >
  Repurpose long-form content into Twitter/X posts and threads. Use when the user
  wants to create tweet threads or standalone tweets from a blog post, video
  transcript, podcast, or other source material. Optimizes for engagement and
  shareability within character limits.
---

# Twitter/X Content Repurposer

Take any long-form content and produce Twitter/X-optimized standalone tweets and threads that stand alone, drive engagement, and match the creator's brand voice. Each run surfaces the 2-3 highest-value insights from the source and writes both a standalone tweet and a full thread per insight — giving you a ready-to-publish content batch from a single piece of source material.

## Capabilities

- Accept a YouTube URL, blog/article URL, file path, or pasted text as the source
- Detect input type automatically and retrieve content without manual copy-paste
- Identify the 2-3 insights with the highest Twitter fit from the source
- Write a standalone tweet and a thread per insight
- Apply brand voice if `brand-voice.md` is configured; fall back to neutral professional tone
- Save output to the configured destination folder with consistent naming
- Run first-run checks for config and brand voice before generating anything

## How to Use

Invoke this skill with any of the following:

- `Repurpose this into tweets`
- `Create a Twitter thread from [URL]`
- `Write tweets from my blog post at [file path]`
- `Turn this transcript into Twitter content`
- `/repurpose-twitter`

## Input Parameters

| Parameter | Required | Description |
|-----------|----------|-------------|
| Source file or URL | Yes (or auto) | File path, YouTube URL, blog/article URL, or pasted text. If omitted, uses the most recent source in the configured folder. |
| Brand voice | No | Automatically loaded from `brand-voice.md` if present. Override by passing voice notes directly in the message. |

## Output

A markdown file saved to the configured destination folder:

```
{source-title}-twitter-{YYYY-MM-DD}.md
```

The file contains:
- 2-3 selected insights with a brief rationale for each
- A standalone tweet and a full thread per insight
- A source reference linking back to the original content

---

## Phase 1: First-Run Checks

Before ingesting any content, run these checks in order.

### Check 1: repurposer-config.md

Look for the config file at:

```
~/.claude/plugins/swayam-dhawan-creatofy-skill/repurposer-config.md
```

If the file does not exist, run the `setup-repurposer` skill inline before proceeding. Do not continue until the config is in place.

If the file exists, read it and note the configured destination folder for output delivery.

### Check 2: brand-voice.md

Look for the brand voice file at:

```
~/.claude/plugins/swayam-dhawan-creatofy-skill/brand-voice.md
```

**If the file does not exist**, pause and offer setup:

> "Want to set up your brand voice first? It takes ~3 minutes and makes every output sound like you. Or skip for now and I'll use a neutral professional tone."

Wait for the user's response before continuing.

- If the user wants to set up brand voice: run the `setup-voice` skill inline, then return to Phase 2.
- If the user skips: proceed to Phase 2 with neutral professional tone. Add a reminder at the bottom of the output file:

  *"Tip: Run `/setup-voice` to make future outputs match your brand."*

**If the file exists**, load it now. It will govern tone, vocabulary, and style in all tweet and thread variations.

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

## Phase 3: Analyze for Twitter

Read the normalized source content and identify tweetable moments. For each candidate, evaluate its fit for Twitter using this table:

| Insight Type | What to Look For | Twitter Fit |
|-------------|-----------------|-------------|
| Surprising stat | Unexpected numbers, counterintuitive findings, trend reversals | High — stat tweets stop the scroll |
| Hot take | Contrarian position, unpopular opinion, challenge to consensus | High — provocation drives quote-tweets |
| One-liner insight | A single idea sharp enough to stand alone in 280 chars | High — quotable, shareable, retweetable |
| List item | A numbered item or framework step that stands alone as advice | Medium — thread-worthy, save-worthy |
| Quotable moment | A punchy phrase, memorable analogy, or vivid illustration | High — screenshot-worthy content |

**Selection criteria:**

- The insight must land in a single tweet without requiring context from the source
- It must be specific, concrete, and immediately credible
- It should provoke a reaction: retweet, quote-tweet, bookmark, or reply

**Select 2-3 best insights for Twitter.** More than 3 dilutes quality. If the source is rich, note the remaining candidates at the end of the output so the user can request additional tweets or threads.

---

## Phase 4: Generate Content

For each selected insight, write two pieces of content: a standalone tweet and a full thread.

### 1. Standalone Tweet

- Single tweet, under 280 characters
- Designed for maximum retweetability: sharp, specific, and self-contained
- No thread indicator (no "1/", no "🧵") — this stands completely alone
- Ends without a CTA — the insight itself is the value

### 2. Thread

A complete thread with the following structure:

**Hook tweet**
- Bold claim, surprising stat, or open loop question that demands the thread be read
- Under 280 characters
- Does not give away the payoff — creates a curiosity gap
- Often uses patterns like: "Most people don't know...", "Unpopular opinion:", "Here's what I learned after..."

**Value tweets (3-5 tweets)**
- One insight per tweet, each under 280 characters
- Each tweet delivers standalone value — a reader dropping into tweet 3 should not be lost
- Use reply hooks to maintain momentum: "Here's the thing most people miss →", "But here's where it gets interesting:", "This is the part nobody talks about:"
- Optimize for quote-tweet-ability — each tweet should be worth screenshotting on its own

**CTA tweet**
- Closes the thread with a follow, link, or engagement ask
- Natural and conversational — not promotional-sounding
- Examples: "What would you add?", "Follow for more on [topic]", "Full breakdown at [link]"

### Rules for All Tweets and Threads

- Every individual tweet is strictly under 280 characters
- No hashtags in tweet body (Twitter's algorithm deprioritizes hashtag-heavy tweets)
- If `brand-voice.md` exists, match tone, vocabulary register, and structural tendencies from the profile
- Threads use numbered format for value tweets (e.g., "2/", "3/") to aid navigation

### Tweet Block Format

Use this structure for each tweet block in the output file:

```markdown
## Insight [X]: [Brief insight label]

*Source moment: [One-sentence description of where this came from in the source]*

---

### Standalone Tweet

[Tweet text — under 280 characters]

---

### Thread

**Hook**
[Hook tweet — under 280 characters]

**2/**
[Value tweet — under 280 characters]

**3/**
[Value tweet — under 280 characters]

**4/**
[Value tweet — under 280 characters]

**5/**
[Value tweet — under 280 characters]

**CTA**
[CTA tweet — under 280 characters]
```

---

## Phase 5: Deliver Output

### File Naming

Save the output file to the destination folder specified in `repurposer-config.md`:

```
{source-title}-twitter-{YYYY-MM-DD}.md
```

- `source-title`: Slugified version of the source title (lowercase, hyphens, no special characters)
- `YYYY-MM-DD`: Today's date

### File Structure

```markdown
---
source_title: [title]
source_url: [URL or file path]
generated: [YYYY-MM-DD]
---

# Twitter/X Posts: [Source Title]

[Tweet blocks for each insight — see Phase 4 format]

---

## Remaining Candidates

If additional insights were identified but not developed, list them here with a one-line description so the user can request tweets or threads for any of them.

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

> "Your tweets and threads are ready. Here's what else you can do with the same source content:
>
> - `/repurpose-linkedin` — LinkedIn post variations
> - `/repurpose-email` — newsletter section or standalone email
> - `/repurpose-blog` — SEO-optimized blog post draft
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

- [ ] Every tweet under 280 characters
- [ ] Thread hook creates curiosity gap
- [ ] Each thread tweet stands alone as value
- [ ] CTA is natural, not forced
- [ ] Matches brand voice (if configured)
- [ ] Source reference appended
- [ ] No hashtags in any tweet body
- [ ] Output file saved to configured destination with correct naming convention
- [ ] Brand voice reminder appended if setup was skipped
