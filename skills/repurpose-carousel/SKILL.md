---
name: repurpose-carousel
description: >
  Repurpose long-form content into carousel slide content (LinkedIn, Instagram).
  Use when the user wants to create a swipeable carousel from a blog post, video
  transcript, podcast, or other source material. Generates slide text and design
  direction for 7-10 slides.
---

# Carousel Content Repurposer

Take any long-form content and produce a swipeable carousel optimized for LinkedIn or Instagram. Each run extracts the single best teachable framework from the source and builds 7-10 slides around it — giving you a save-worthy, share-worthy carousel ready for design from a single piece of source material.

## Capabilities

- Accept a YouTube URL, blog/article URL, file path, or pasted text as the source
- Detect input type automatically and retrieve content without manual copy-paste
- Identify the single best framework, process, or step-by-step from the source
- Generate a cover slide, 5-8 content slides, and a CTA slide
- Include a design direction note for every slide (icon, diagram, text-only, etc.)
- Apply brand voice if `brand-voice.md` is configured; fall back to neutral professional tone
- Save output to the configured destination folder with consistent naming
- Run first-run checks for config and brand voice before generating anything

## How to Use

Invoke this skill with any of the following:

- `Repurpose this into a carousel`
- `Create a carousel from [URL]`
- `Turn this into a LinkedIn carousel`
- `Make an Instagram carousel from my blog post at [file path]`
- `/repurpose-carousel`

## Input Parameters

| Parameter | Required | Description |
|-----------|----------|-------------|
| Source file or URL | Yes (or auto) | File path, YouTube URL, blog/article URL, or pasted text. If omitted, uses the most recent source in the configured folder. |
| Brand voice | No | Automatically loaded from `brand-voice.md` if present. Override by passing voice notes directly in the message. |

## Output

A markdown file saved to the configured destination folder:

```
{source-title}-carousel-{YYYY-MM-DD}.md
```

The file contains:
- The selected framework with a brief rationale for choosing it
- 7-10 slides: cover, content slides, and CTA slide
- Each slide has: headline, supporting copy (max 2 lines), and a design direction note
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

**If the file exists**, load it now. It will govern tone, vocabulary, and style in all slide copy.

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

## Phase 3: Extract Teachable Framework

Read the normalized source content and identify the single best framework, process, or step-by-step that can anchor the carousel. Look for:

| Structure Type | What to Look For | Carousel Fit |
|---------------|-----------------|--------------|
| Numbered list | Explicit steps, ranked items, ordered sequences | High — maps directly to slides |
| How-to section | Instructions, tutorials, walkthroughs | High — each step becomes a slide |
| Comparison framework | Side-by-side contrasts, before/after, option A vs B | High — visual structure ready-made |
| Before/after progression | State change, transformation, outcome arc | High — narrative momentum drives swipes |
| Process or system | Repeatable workflow, methodology, formula | Medium — abstract into named stages |

**Selection criteria:**

- The framework must have a clear sequence or structure the viewer can follow slide by slide
- Each element of the framework must be explainable in a headline plus 1-2 lines
- The framework should deliver a complete, standalone lesson without requiring the viewer to read the source

**Select 1 framework.** Carousels work best when they teach one thing well. If the source contains multiple strong candidates, note the others at the end of the output so the user can request additional carousels.

---

## Phase 4: Generate Carousel Slides

Build 7-10 slides around the selected framework. Every slide must have:
- A headline (short, scannable, under 8 words where possible)
- Supporting copy: 1-2 lines maximum. One idea per slide.
- A design direction note: actionable visual suggestion for the designer or design tool

### Slide Structure

**Slide 1: Cover Slide**
- Headline: a hook using curiosity or a clear benefit — must create swipe motivation
- Subtitle: one supporting line that gives context or tease
- Design direction: suggest background treatment, typography emphasis, or visual element (e.g., bold text on gradient, illustrated icon as accent, photo background with text overlay)

**Slides 2–[N-1]: Content Slides (5–8 slides)**
- Each slide covers exactly one idea, one step, or one element of the framework
- Headline states the idea directly — no clever obscurity
- Supporting copy (1-2 lines): expands with a stat, example, or short explanation
- Design direction: specify a visual approach for this slide (icon, screenshot, diagram, bullet list, text-only, quote callout, etc.)

**Slide [Last]: CTA Slide**
- One clear call to action: follow, save, share, or link
- Optional: reinforce the value of what they just learned in one line
- Design direction: suggest a clean, high-contrast treatment to make the CTA stand out

### Rules for All Slides

- Headline + copy must be readable in 3 seconds
- No slide should require reading the previous slide to make sense
- If `brand-voice.md` exists, match tone, vocabulary register, and structural tendencies from the profile
- Design directions are suggestions, not mandates — but they must be specific enough to act on

### Slide Block Format

Use this structure for each slide block in the output file:

```markdown
## Slide [N]: [Slide type — Cover / Content / CTA]

**Headline:** [Slide headline]

**Copy:** [1-2 lines of supporting text, or "None" for text-only headline slides]

**Design direction:** [Specific visual suggestion]
```

---

## Phase 5: Deliver Output

### File Naming

Save the output file to the destination folder specified in `repurposer-config.md`:

```
{source-title}-carousel-{YYYY-MM-DD}.md
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

# Carousel: [Source Title]

## Selected Framework

**Framework:** [Name or short description]
**Rationale:** [One sentence explaining why this was chosen over other candidates]

---

[Slide blocks for each slide — see Phase 4 format]

---

## Additional Framework Candidates

If other strong frameworks were identified but not developed, list them here with a one-line description so the user can request additional carousels.

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

> "Your carousel is ready. Here's what else you can do with the same source content:
>
> - `/repurpose-linkedin` — LinkedIn post variations
> - `/repurpose-twitter` — thread and individual tweet variations
> - `/repurpose-email` — newsletter section or standalone email
> - `/repurpose-blog` — SEO-optimized blog post draft
> - `/repurpose-shorts` — TikTok and YouTube Shorts scripts
> - `/repurpose-all` — run all formats at once
>
> Run `/setup-voice` anytime to lock in your brand voice."

Then set `first_run_complete` to `true` in `repurposer-config.md`.

If `first_run_complete` is already `true`, skip this message.

---

## Quality Checklist

Before saving and delivering the output file, verify each item:

- [ ] Cover slide hook creates swipe motivation
- [ ] One idea per content slide
- [ ] Each slide has headline + max 2 lines
- [ ] 7-10 slides total
- [ ] Design direction is actionable
- [ ] CTA slide is specific
- [ ] Matches brand voice (if configured)
- [ ] Source reference appended at the bottom of the output file
- [ ] Output file saved to configured destination with correct naming convention
- [ ] Brand voice reminder appended if setup was skipped
