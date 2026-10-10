---
name: repurpose-linkedin
description: >
  Repurpose long-form content into LinkedIn posts. Use when the user wants to
  create LinkedIn content from a blog post, video transcript, podcast, or other
  source material. Generates multiple post variations with hooks and CTAs.
  Accepts a file, URL, or uses the most recent source in the configured folder.
---

# LinkedIn Content Repurposer

Take any long-form content and produce LinkedIn-optimized post variations that stand alone, drive engagement, and match the creator's brand voice. Each run surfaces the 2-3 highest-value insights from the source and writes three post variations per insight — giving you a ready-to-publish content batch from a single piece of source material.

## Capabilities

- Accept a YouTube URL, blog/article URL, file path, or pasted text as the source
- Detect input type automatically and retrieve content without manual copy-paste
- Identify the 2-3 insights with the highest LinkedIn fit from the source
- Write three post variations per insight (hook-heavy, story-driven, data-driven)
- Apply brand voice if `brand-voice.md` is configured; fall back to neutral professional tone
- Save output to the configured destination folder with consistent naming
- Run first-run checks for config and brand voice before generating anything

## How to Use

Invoke this skill with any of the following:

- `Repurpose this into LinkedIn posts`
- `Create LinkedIn content from [URL]`
- `Write LinkedIn posts from my blog post at [file path]`
- `Turn this transcript into LinkedIn content`
- `/repurpose-linkedin`

## Input Parameters

| Parameter | Required | Description |
|-----------|----------|-------------|
| Source file or URL | Yes (or auto) | File path, YouTube URL, blog/article URL, or pasted text. If omitted, uses the most recent source in the configured folder. |
| Brand voice | No | Automatically loaded from `brand-voice.md` if present. Override by passing voice notes directly in the message. |

## Output

A markdown file saved to the configured destination folder:

```
{source-title}-linkedin-{YYYY-MM-DD}.md
```

The file contains:
- 2-3 selected insights with a brief rationale for each
- 3 post variations per insight (hook-heavy, story-driven, data-driven)
- Hashtag suggestions listed at the end of each post block
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

**If the file exists**, load it now. It will govern tone, vocabulary, and style in all post variations.

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

## Phase 3: Analyze for LinkedIn

Read the normalized source content and identify standalone insights. For each candidate, evaluate its fit for LinkedIn using this table:

| Insight Type | What to Look For | LinkedIn Fit |
|-------------|-----------------|--------------|
| Data point or stat | Numbers, percentages, trends | High — stat hooks drive engagement |
| Contrarian take | Challenges to common beliefs | High — debate drives comments |
| Framework or process | Step-by-step methods | Medium — save-worthy content |
| Story or anecdote | Personal examples, case studies | High — narrative hooks |
| Quick tip | Actionable in under 2 minutes | Medium — utility content |

**Selection criteria:**

- The insight must stand alone without requiring the reader to know the source
- It must be specific enough to be immediately credible
- It should invite a reaction: agreement, disagreement, curiosity, or application

**Select 2-3 best insights for LinkedIn.** More than 3 dilutes quality. If the source is rich, note the remaining candidates at the end of the output so the user can request additional posts.

---

## Phase 4: Generate Post Variations

For each selected insight, write 3 variations. Each variation is a complete, self-contained LinkedIn post.

### Variation Types

**1. Hook-heavy**
- Opens with a bold claim, surprising statement, or direct question
- Uses short, punchy lines — one idea per line
- Builds quickly to the payoff, then closes with a question or CTA
- Reads like the writer is interrupting the scroll with a provocation

**2. Story-driven**
- Opens with a scene-setter: "Last week...", "I used to think...", "Three years ago..."
- Develops a mini-narrative arc (setup, tension, resolution)
- Delivers the insight as the lesson learned, not as the opening statement
- Closes by connecting the story back to the reader's situation

**3. Data-driven**
- Leads with the stat, number, or specific finding
- Unpacks what the data actually means — what the implication is, not just the number
- Uses the insight as a lens to interpret a broader trend or behavior
- Closes with a question that invites the reader to weigh in

### Rules for All Variations

- Under 300 words per post
- Liberal line breaks — no walls of text; single-sentence paragraphs are encouraged
- First line must hook without needing context from the source
- Ends with a CTA or open question that drives comments
- No hashtags in the post body — hashtag suggestions are listed separately after each post block
- If `brand-voice.md` exists, match tone, vocabulary register, and structural tendencies from the profile

### Post Block Format

Use this structure for each post block in the output file:

```markdown
## Insight [X]: [Brief insight label]

*Source moment: [One-sentence description of where this came from in the source]*

---

### Variation 1: Hook-Heavy

[Post text]

**Hashtags:** #[tag] #[tag] #[tag]

---

### Variation 2: Story-Driven

[Post text]

**Hashtags:** #[tag] #[tag] #[tag]

---

### Variation 3: Data-Driven

[Post text]

**Hashtags:** #[tag] #[tag] #[tag]
```

---

## Phase 5: Deliver Output

### File Naming

Save the output file to the destination folder specified in `repurposer-config.md`:

```
{source-title}-linkedin-{YYYY-MM-DD}.md
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

# LinkedIn Posts: [Source Title]

[Post blocks for each insight — see Phase 4 format]

---

## Remaining Candidates

If additional insights were identified but not developed, list them here with a one-line description so the user can request posts for any of them.

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

> "Your LinkedIn posts are ready. Here's what else you can do with the same source content:
>
> - `/repurpose-twitter` — thread and individual tweet variations
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

- [ ] Each post stands alone (does not require the reader to know the source)
- [ ] Every post is under 300 words
- [ ] Every post's first line hooks without context
- [ ] Every post ends with an engagement driver (question or CTA)
- [ ] No hashtags appear in the post body (only in the Hashtags line below each post)
- [ ] Brand voice applied if `brand-voice.md` exists; neutral tone used if not
- [ ] Source reference appended at the bottom of the output file
- [ ] Output file saved to configured destination with correct naming convention
- [ ] Brand voice reminder appended if setup was skipped
