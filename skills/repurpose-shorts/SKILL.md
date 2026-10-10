---
name: repurpose-shorts
description: >
  Repurpose long-form content into short-form video scripts (TikTok, Reels,
  YouTube Shorts). Use when the user wants 30-60 second video scripts from a
  blog post, video transcript, podcast, or other source material. Identifies
  hook moments and writes teleprompter-ready scripts.
---

# Short-Form Video Script Repurposer

Take any long-form content and produce teleprompter-ready short-form video scripts for TikTok, Instagram Reels, and YouTube Shorts. Each run identifies the 3-5 highest-value hook moments from the source and writes a complete script per moment — giving you a ready-to-record content batch from a single piece of source material.

## Capabilities

- Accept a YouTube URL, blog/article URL, file path, or pasted text as the source
- Detect input type automatically and retrieve content without manual copy-paste
- Identify the 3-5 moments with the highest short-form hook potential from the source
- Write one complete script per moment, formatted for teleprompter delivery
- Label each script by platform fit: TikTok / Reel / YouTube Short / Universal
- Apply brand voice if `brand-voice.md` is configured; fall back to neutral conversational tone
- Save output to the configured destination folder with consistent naming
- Run first-run checks for config and brand voice before generating anything

## How to Use

Invoke this skill with any of the following:

- `Repurpose this into short-form video scripts`
- `Create TikTok scripts from [URL]`
- `Write Shorts scripts from my blog post at [file path]`
- `Turn this transcript into video shorts`
- `/repurpose-shorts`

## Input Parameters

| Parameter | Required | Description |
|-----------|----------|-------------|
| Source file or URL | Yes (or auto) | File path, YouTube URL, blog/article URL, or pasted text. If omitted, uses the most recent source in the configured folder. |
| Brand voice | No | Automatically loaded from `brand-voice.md` if present. Override by passing voice notes directly in the message. |

## Output

A markdown file saved to the configured destination folder:

```
{source-title}-shorts-{YYYY-MM-DD}.md
```

The file contains:
- 3-5 selected hook moments with a brief rationale for each
- One complete teleprompter-ready script per moment (hook, body, CTA)
- Platform label for each script (TikTok / Reel / YouTube Short / Universal)
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

> "Want to set up your brand voice first? It takes ~3 minutes and makes every output sound like you. Or skip for now and I'll use a neutral conversational tone."

Wait for the user's response before continuing.

- If the user wants to set up brand voice: run the `setup-voice` skill inline, then return to Phase 2.
- If the user skips: proceed to Phase 2 with neutral conversational tone. Add a reminder at the bottom of the output file:

  *"Tip: Run `/setup-voice` to make future outputs match your brand."*

**If the file exists**, load it now. It will govern tone, vocabulary, and style in all scripts.

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

## Phase 3: Identify Hook Moments

Read the normalized source content and identify moments with strong short-form potential. For each candidate, evaluate its hook type using this table:

| Moment Type | What to Look For | Why It Works |
|-------------|-----------------|--------------|
| Surprising stat | Numbers that make you double-take | Stat hooks are #1 short-form opener |
| Contrarian take | Challenges conventional wisdom | Debate-bait drives comments/shares |
| Quick tip / hack | Actionable in under 30 seconds | Utility content gets saved/shared |
| Story / anecdote | Mini-stories or examples | Narrative hooks hold attention |
| Before/after | Comparisons or outcome reveals | Transformation is short-form gold |
| List item standalone | Individual items from lists | Each can be its own short |
| Quotable line | Punchy one-liners | Text overlay hooks |

**Selection criteria:**

- The moment must stand alone without requiring the viewer to know the source
- It must deliver complete value in 30-60 seconds
- It should grab attention in the first 3 seconds

**Select 3-5 best moments for short-form.** More than 5 dilutes quality. If the source is rich, note the remaining candidates at the end of the output so the user can request additional scripts.

---

## Phase 4: Write Short-Form Scripts

For each selected moment, write one complete script. Each script is formatted for teleprompter delivery and labeled by platform.

### Script Structure

Every script follows this structure:

**Hook (first 3 seconds):** A bold statement, direct question, or visual cue that stops the scroll. Written as a single punchy line the creator reads directly to camera.

**Body (20-50 seconds):** The core insight delivered with pacing in mind. Written in short lines with `[PAUSE]` markers where the speaker should breathe or let a point land. Include `[B-ROLL]` cues where cutaway footage would strengthen the visual.

**CTA (last 5 seconds):** A single, clear call to action. Follow, comment with a specific response, or reference a link. One CTA only — do not stack.

### Teleprompter Formatting Rules

- Short lines: one idea per line, maximum 8-10 words per line
- `[PAUSE]` on its own line wherever a beat is needed
- `[B-ROLL: description]` on its own line for visual cue suggestions
- No walls of text — the creator should be able to glance and read naturally
- Write in the creator's speaking voice, not formal written prose

### Platform Labels

Assign one or more labels to each script based on fit:

- **TikTok** — personality-forward, casual, hook must land in 2 seconds, comment-bait encouraged
- **Reel** — similar to TikTok but slightly more polished; aesthetic quality matters
- **YouTube Short** — slightly more informational; hook can land in 3 seconds; works as a funnel to long-form
- **Universal** — works across all three with minimal adjustment

### Script Block Format

Use this structure for each script block in the output file:

```markdown
## Short #[X]: [Brief moment label]

*Source moment: [One-sentence description of where this came from in the source]*
*Platform: TikTok / Reel / YouTube Short / Universal*
*Estimated read time: [X seconds]*

---

**HOOK (0-3 seconds)**

[Hook line]

[PAUSE]

**BODY ([X]-[X] seconds)**

[Line 1]

[PAUSE]

[Line 2]

[B-ROLL: description of suggested visual]

[Line 3]

[PAUSE]

[Line 4]

**CTA (last 5 seconds)**

[CTA line]
```

---

## Phase 5: Deliver Output

### File Naming

Save the output file to the destination folder specified in `repurposer-config.md`:

```
{source-title}-shorts-{YYYY-MM-DD}.md
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

# Short-Form Scripts: [Source Title]

[Script blocks for each moment — see Phase 4 format]

---

## Remaining Candidates

If additional moments were identified but not developed, list them here with a one-line description so the user can request scripts for any of them.

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

> "Your short-form scripts are ready. Here's what else you can do with the same source content:
>
> - `/repurpose-linkedin` — LinkedIn post variations
> - `/repurpose-twitter` — thread and individual tweet variations
> - `/repurpose-email` — newsletter section or standalone email
> - `/repurpose-blog` — SEO-optimized blog post draft
> - `/repurpose-carousel` — LinkedIn or Instagram carousel outline
> - `/repurpose-all` — run all formats at once
>
> Run `/setup-voice` anytime to lock in your brand voice."

Then set `first_run_complete` to `true` in `repurposer-config.md`.

If `first_run_complete` is already `true`, skip this message.

---

## Quality Checklist

Before saving and delivering the output file, verify each item:

- [ ] Each script is 30-60 seconds when read aloud
- [ ] Hook grabs attention in first 3 seconds
- [ ] Teleprompter-formatted (short lines, pause markers)
- [ ] Platform labeled (TikTok / Reel / YouTube Short / Universal)
- [ ] Matches brand voice (if configured); neutral conversational tone if not
- [ ] Source reference appended at the bottom of the output file
- [ ] Output file saved to configured destination with correct naming convention
- [ ] Brand voice reminder appended if setup was skipped
