---
name: repurpose-email
description: >
  Repurpose long-form content into an email newsletter draft. Use when the user
  wants to create a newsletter from a blog post, video transcript, podcast, or
  other source material. Generates subject lines, preview text, body, and CTA.
  Optionally creates a Gmail draft if the connector is available.
---

# Email Newsletter Repurposer

Take any long-form content and produce a focused, conversion-oriented email newsletter draft. Each run identifies the single most compelling takeaway from the source and builds a complete newsletter around it — with subject line variations, preview text, and a fully written body in both plain-text and HTML-friendly formats.

## Capabilities

- Accept a YouTube URL, blog/article URL, file path, or pasted text as the source
- Detect input type automatically and retrieve content without manual copy-paste
- Identify the single most compelling takeaway with the highest email fit
- Write 3 subject line variations (curiosity, benefit, direct)
- Generate preview text that complements — not repeats — the subject
- Write a complete email body using inverted pyramid structure
- Apply brand voice if `brand-voice.md` is configured; fall back to neutral conversational tone
- Deliver both plain-text and HTML-friendly versions
- Save output to the configured destination folder with consistent naming
- Offer to create a Gmail draft if the Gmail connector is available
- Run first-run checks for config and brand voice before generating anything

## How to Use

Invoke this skill with any of the following:

- `Repurpose this into an email newsletter`
- `Create an email from [URL]`
- `Write a newsletter from my blog post at [file path]`
- `Turn this transcript into an email`
- `/repurpose-email`

## Input Parameters

| Parameter | Required | Description |
|-----------|----------|-------------|
| Source file or URL | Yes (or auto) | File path, YouTube URL, blog/article URL, or pasted text. If omitted, uses the most recent source in the configured folder. |
| Brand voice | No | Automatically loaded from `brand-voice.md` if present. Override by passing voice notes directly in the message. |

## Output

A markdown file saved to the configured destination folder:

```
{source-title}-email-{YYYY-MM-DD}.md
```

The file contains:
- The selected takeaway with a brief rationale
- 3 subject line variations (curiosity, benefit, direct)
- Preview text (1-2 lines)
- Plain-text email body
- HTML-friendly email body (with bold, links, section breaks)
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

> "Want to set up your brand voice first? It takes ~3 minutes and makes every output sound like you. Or skip for now and I'll use a neutral conversational tone."

Wait for the user's response before continuing.

- If the user wants to set up brand voice: run the `setup-voice` skill inline, then return to Phase 2.
- If the user skips: proceed to Phase 2 with neutral conversational tone. Add a reminder at the bottom of the output file:

  *"Tip: Run `/setup-voice` to make future outputs match your brand."*

**If the file exists**, load it now. It will govern tone, vocabulary, and style in the email draft.

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

If `brand-voice.md` exists (confirmed in Phase 1), it is already loaded. Apply it in all writing phases below. If it does not exist, use neutral conversational tone throughout.

---

## Phase 3: Analyze for Email

Read the normalized source content and identify the single most compelling takeaway for email. Email is about one focused message — not multiple insights. Choose one takeaway and build the entire newsletter around it.

Evaluate candidates using this table:

| Takeaway Type | What to Look For | Email Fit |
|--------------|-----------------|-----------|
| Surprising insight | Challenges an assumption the reader likely holds | High — creates the "I had no idea" open |
| Practical how-to | A specific action the reader can take this week | High — drives clicks and replies |
| Data point or stat | A number that reframes a familiar situation | Medium-High — works when tied to a clear implication |
| Story with a lesson | A narrative arc that ends in a transferable takeaway | High — personal tone fits email well |
| Opinion or perspective | A clear stance the writer holds with reasoning | Medium — works when the reader trusts the sender |

**Selection criteria:**

- The takeaway must be specific enough to anchor an entire email
- It must have a clear implication for the reader — not just interesting, but useful or provocative
- It should support a single, unambiguous CTA (click, reply, share, try something)

**Select 1 primary takeaway.** Also identify 2-3 supporting points that reinforce or contextualize it — these will populate the email body. Note any remaining interesting points at the end of the output as candidates for future sends.

---

## Phase 4: Generate Newsletter Draft

Write the complete email newsletter draft. Email is more personal than other formats — write as if the sender is talking directly to one reader, not broadcasting to a list.

### Subject Lines

Write 3 subject line variations. Each must be under 60 characters.

**1. Curiosity**
- Opens a question the reader wants answered
- Withholds the payoff — the answer is in the email
- Example pattern: "The thing nobody tells you about [topic]"

**2. Benefit**
- Leads with what the reader gains by opening
- Specific and concrete — not vague promises
- Example pattern: "How to [outcome] without [common obstacle]"

**3. Direct**
- States the takeaway plainly
- Works when the insight itself is surprising or counterintuitive
- Example pattern: "[Specific claim or finding]"

### Preview Text

Write 1-2 lines (under 100 characters combined) that complement the subject line. Preview text must not repeat the subject — it should add information that increases the likelihood of an open.

### Email Body: Inverted Pyramid Structure

Structure the body in this order:

**1. Key Takeaway First (top of pyramid)**
- Open with the most important point — do not bury the lead
- One short paragraph or 2-3 punchy lines
- The reader should understand the core message within the first 3 sentences

**2. Supporting Detail (middle of pyramid)**
- 2-3 supporting points that add context, evidence, or nuance
- Keep each point brief — 2-4 sentences
- Use a brief transition between points to maintain flow
- This is where the story, data, or how-to lives

**3. CTA (bottom of pyramid)**
- One clear call to action — do not offer multiple options
- Make it specific: what to click, what to reply, what to try
- Optional: one closing sentence that reinforces the value of acting

### Tone Guidelines

- Slightly more personal and conversational than other formats
- Write in second person ("you") throughout
- Avoid corporate or overly formal language
- Short sentences. Short paragraphs. No walls of text.
- If `brand-voice.md` exists, match its tone while keeping the conversational register

### Two Versions

Write the email body in two versions:

**Plain-Text Version**
- No markdown formatting
- Line breaks between paragraphs
- URLs written out in full
- Clean and readable in any email client

**HTML-Friendly Version**
- Bold key phrases using `**bold**`
- Use section breaks (`---`) between major sections
- Format any links as `[anchor text](URL)` where applicable
- Use short bulleted lists for supporting points if appropriate

### Email Draft Block Format

Use this structure in the output file:

```markdown
## Selected Takeaway

*[One-sentence description of the core takeaway and why it was chosen for email]*

**Supporting points:**
- [Point 1]
- [Point 2]
- [Point 3]

---

## Subject Lines

**Curiosity:** [Subject line — under 60 chars]
**Benefit:** [Subject line — under 60 chars]
**Direct:** [Subject line — under 60 chars]

## Preview Text

[1-2 lines, under 100 characters combined]

---

## Plain-Text Version

[Full email body — plain text, no markdown]

---

## HTML-Friendly Version

[Full email body — with bold, links, section breaks]
```

---

## Phase 5: Deliver Output

### File Naming

Save the output file to the destination folder specified in `repurposer-config.md`:

```
{source-title}-email-{YYYY-MM-DD}.md
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

# Email Newsletter: [Source Title]

[Email draft block — see Phase 4 format]

---

## Remaining Candidates

If additional takeaways were identified but not developed, list them here with a one-line description so the user can request a second email send for any of them.

---

## Source

[Title] — [URL or file path]
```

### Gmail Draft (if connector available)

After saving the output file, check whether the Gmail connector (`mcp__claude_ai_Gmail__gmail_create_draft`) is available.

If it is available, offer:

> "Want me to create a Gmail draft with one of these subject lines? Just say which variation you prefer (curiosity, benefit, or direct) and I'll create the draft now."

If the user confirms, call `mcp__claude_ai_Gmail__gmail_create_draft` using:
- The chosen subject line
- The HTML-friendly body version
- No recipient (draft only — the user will address it before sending)

If the connector is not available, skip this step silently.

### Append Brand Voice Reminder (if applicable)

If the user skipped brand voice setup in Phase 1, append this line at the bottom of the file:

*"Tip: Run `/setup-voice` to make future outputs match your brand."*

---

## Phase 6: Post-First-Run Message

After delivering the output, check the `first_run_complete` flag in `repurposer-config.md`.

If `first_run_complete` is `false`, show this message:

> "Your email newsletter draft is ready. Here's what else you can do with the same source content:
>
> - `/repurpose-linkedin` — LinkedIn post variations
> - `/repurpose-twitter` — thread and individual tweet variations
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

- [ ] Subject lines are under 60 chars
- [ ] Preview text doesn't repeat the subject
- [ ] Body follows inverted pyramid (key takeaway first, then support, then CTA)
- [ ] Single clear CTA
- [ ] Both plain-text and HTML versions provided
- [ ] Matches brand voice (if configured); neutral conversational tone if not
- [ ] Source reference appended at the bottom of the output file
- [ ] Output file saved to configured destination with correct naming convention
- [ ] Gmail draft offered if connector is available
- [ ] Brand voice reminder appended if setup was skipped
