---
name: repurpose-all
description: >
  Repurpose long-form content into all configured formats at once. Use when the
  user wants to generate LinkedIn, Twitter, email, blog, short-form video, and
  carousel content from a single source. Dispatches format skills in parallel.
  Can be attached to a scheduled task for automated repurposing.
---

# Content Repurposer: All Formats

Take any long-form content source and generate all six format outputs in a single run. Ingests the source once, runs setup checks, then dispatches one sub-agent per format simultaneously. Returns a summary table when all formats complete, with partial success handling if any format fails.

## Capabilities

- Runs all 6 format skills: LinkedIn, Twitter, Email, Blog, Shorts, Carousel
- Ingests the source content once and shares it across all format sub-agents
- Handles first-run setup inline if config or brand voice is missing
- Supports selecting a subset of formats to generate
- Dispatches all format sub-agents in parallel for fast turnaround
- Reports partial success: which formats succeeded, which failed, and why
- Logs all results to `processed-log.md`
- Can be attached to a scheduled task for fully automated repurposing

## How to Use

Invoke this skill with any of the following:

- `Repurpose this into everything`
- `Repurpose all from [URL]`
- `Run the weekly repurpose`
- `Generate all formats from [file path]`
- `/repurpose-all`

## Input Parameters

| Parameter | Required | Description |
|-----------|----------|-------------|
| Source file or URL | Yes (or auto) | File path, YouTube URL, blog/article URL, or pasted text. If omitted, uses the most recent unprocessed source in the configured folder. |
| Formats to include | No | Comma-separated list of formats to generate (e.g., `linkedin, twitter, email`). Defaults to all 6, or the defaults configured in `repurposer-config.md`. |
| Formats to exclude | No | Comma-separated list of formats to skip (e.g., `carousel`). Overrides defaults. |

## Output

All six format files are saved to the configured destination folder, plus:

- A summary table showing status, file name, and word count for each format
- An appended entry in `processed-log.md`
- Retry offer for any failed formats

---

## Phase 1: Ingest

Run the shared ingestion flow once before dispatching any format sub-agents. Reference `shared/ingestion.md` for the full ingestion specification.

### Step 1: Detect Input Type

Inspect what the user provided:

| Input type | Indicators | Action |
|------------|-----------|--------|
| YouTube URL | `youtube.com/watch`, `youtu.be/` | Fetch transcript via Apify |
| Blog or web URL | Any other `http://` or `https://` URL | Scrape via Firecrawl |
| File path | Path starting with `/` or `~/`, or a local file reference | Read the file directly |
| No input | User has not provided a URL or file | Check configured source folder for most recent unprocessed file |

Check the user's message for a URL or file path before asking for anything. If one is present, proceed immediately.

### Step 2: Fetch Content

Retrieve the content using the appropriate method. On failure, fall back to requesting the text directly from the user. Full fallback handling is in `shared/ingestion.md`.

### Step 3: Parse into source-content.md

Extract the title, body text, key topics (3-5), and word count. For sources over 8,000 words, create a condensed 2,000-word version that preserves all data points, quotable moments, and key arguments. Write the normalized output following the `source-content.md` template in `shared/ingestion.md`.

Save the result as `source-content.md` in the configured working directory. This file will be read by every format sub-agent in Phase 4 — ingestion runs once, not six times.

---

## Phase 2: Setup Checks

Before dispatching format sub-agents, verify configuration is in place. Run these checks in order.

### Check 1: repurposer-config.md

Look for the config file at:

```
~/.claude/plugins/repurposenow/repurposer-config.md
```

If the file does not exist, run the `setup-repurposer` skill inline before proceeding. Do not continue until the config is in place.

If the file exists, read it and note:
- The configured destination folder for output delivery
- The configured default formats (if set)
- The configured source folder (used in scheduling mode)

### Check 2: brand-voice.md

Look for the brand voice file at:

```
~/.claude/plugins/repurposenow/brand-voice.md
```

**If the file does not exist**, pause and offer setup:

> "Want to set up your brand voice first? It takes ~3 minutes and makes every output sound like you. Or skip for now and I'll use a neutral professional tone across all formats."

Wait for the user's response before continuing.

- If the user wants to set up brand voice: run the `setup-voice` skill inline, then return to Phase 3.
- If the user skips: proceed with neutral professional tone. Each output file will include the brand voice reminder tip.

**If the file exists**, note its path. Each format sub-agent will load it independently during generation.

---

## Phase 3: Format Selection

After setup checks pass, determine which formats to generate.

Ask:

> "Generate all 6 formats, or pick specific ones?"

**Default behavior:** Generate all 6 formats, unless `repurposer-config.md` specifies a different default set.

**If the user specified formats in their invocation** (e.g., "repurpose all except carousel"), apply those inclusions/exclusions without prompting.

**The 6 available formats:**

| Format | Skill | Output Type |
|--------|-------|-------------|
| LinkedIn | `repurpose-linkedin` | Post variations with hooks and CTAs |
| Twitter | `repurpose-twitter` | Thread and individual tweet variations |
| Email | `repurpose-email` | Newsletter section or standalone email |
| Blog | `repurpose-blog` | SEO-optimized blog post draft |
| Shorts | `repurpose-shorts` | TikTok and YouTube Shorts scripts |
| Carousel | `repurpose-carousel` | LinkedIn or Instagram carousel outline |

Confirm the selected formats with the user if they are anything other than all 6.

---

## Phase 4: Parallel Dispatch

Dispatch one sub-agent per selected format simultaneously. Do not run formats sequentially — all selected formats start at the same time.

### Sub-Agent Instructions

Each sub-agent receives the following:

1. **Read `source-content.md`** — the normalized source created in Phase 1. Do not re-ingest the original source.
2. **Read `brand-voice.md`** — if it exists, apply it throughout generation. If it does not exist, use neutral professional tone.
3. **Read `repurposer-config.md`** — use the configured destination folder for saving the output file.
4. **Run format-specific generation** — follow the full generation instructions for that format's skill.
5. **Save output file** — use the standard naming convention for the format:

```
{source-title}-{format}-{YYYY-MM-DD}.md
```

Where `source-title` is a slugified version of the source title (lowercase, hyphens, no special characters) and `YYYY-MM-DD` is today's date.

### Sub-Agent Isolation

Each sub-agent operates independently. A failure in one format does not stop the others. Sub-agents do not need to communicate with each other — they all read from the shared `source-content.md` written in Phase 1.

---

## Phase 5: Collect Results and Handle Failures

Wait for all sub-agents to complete, then assess results.

### Success Case

All selected formats completed successfully. Proceed to Phase 6.

### Partial Failure Case

One or more sub-agents failed. Do not roll back successful outputs.

**Report the failure status inline:**

```
LinkedIn: Done
Twitter: Done
Email: FAILED — [brief reason, e.g., "Firecrawl timeout during ingestion retry"]
Blog: Done
Shorts: Done
Carousel: Done
```

**Offer to retry failed formats:**

> "[Format] failed. Want me to retry it now?"

Wait for a response before retrying. A retry runs that format's sub-agent again with the same `source-content.md` — no re-ingestion needed.

### Log Both Outcomes

Whether a format succeeded or failed, it gets logged in `processed-log.md` in Phase 7. Failed formats are noted with status "FAILED" and the error reason.

---

## Phase 6: Deliver Summary

After all sub-agents complete (or time out), present the summary table:

```markdown
## Repurposing Complete

| Format | Status | Output File | Word Count |
|--------|--------|-------------|------------|
| LinkedIn | ✅ | source-title-linkedin-2026-03-23.md | 850 |
| Twitter | ✅ | source-title-twitter-2026-03-23.md | 620 |
| Email | ✅ | source-title-email-2026-03-23.md | 450 |
| Blog | ✅ | source-title-blog-2026-03-23.md | 1200 |
| Shorts | ✅ | source-title-shorts-2026-03-23.md | 900 |
| Carousel | ✅ | source-title-carousel-2026-03-23.md | 400 |
```

For any failed formats, show ❌ in the Status column and leave Word Count blank. Include the failure reason as a note below the table.

---

## Phase 7: Update Processed Log

Append an entry to `processed-log.md` in the configured destination folder.

### If `processed-log.md` exists:

Append the following row to the table:

```markdown
| 2026-03-23 | [source title] | LinkedIn, Twitter, Email, Blog, Shorts, Carousel | ./output/ |
```

For partial runs or failures, list only the formats that succeeded. Add a note column if any formats failed.

### If `processed-log.md` does not exist:

Create it with the header row first, then append the entry:

```markdown
# Processed Content Log

| Date | Source Title | Formats Generated | Output Folder |
|------|-------------|-------------------|---------------|
| 2026-03-23 | [source title] | LinkedIn, Twitter, Email, Blog, Shorts, Carousel | ./output/ |
```

---

## Scheduling

This skill can be attached to a scheduled task for automated repurposing on a recurring basis.

### How Scheduled Runs Work

When invoked by a scheduler rather than a user:

1. **Scan the configured source folder** for files not already listed in `processed-log.md`
2. **Eligible file types:** `.md`, `.txt`, `.pdf`, `.docx`
3. **Pick the most recent unprocessed file** (by file modification date)
4. **Run default formats** from `repurposer-config.md` — do not prompt for format selection
5. **If no unprocessed files are found:** log "no new content" to `processed-log.md` and exit cleanly

### Scheduling Setup

To attach this skill to a scheduled task:

1. Open `repurposer-config.md` and confirm `source_folder` and `output_folder` are set
2. Create a scheduled task that invokes `/repurpose-all` at your desired cadence (e.g., every Monday morning)
3. The skill will automatically pick up new files in the source folder and skip files already in the log

### Scheduled Run Behavior Differences

| Behavior | Manual Run | Scheduled Run |
|----------|-----------|---------------|
| Format selection prompt | Asks user | Uses config defaults, no prompt |
| Brand voice setup offer | Pauses and asks | Skips, uses existing `brand-voice.md` or neutral tone |
| Failure handling | Offers retry | Logs failure and exits; user reviews log |
| Summary delivery | Shows to user | Appends to `processed-log.md` only |

---

## Quality Checklist

Before marking the run complete, verify each item:

- [ ] Source content was ingested once and saved as `source-content.md`
- [ ] `repurposer-config.md` exists and destination folder is noted
- [ ] Brand voice status confirmed (loaded or skipped)
- [ ] Format selection confirmed with user (or defaults applied from config)
- [ ] All selected format sub-agents were dispatched in parallel
- [ ] Each sub-agent read from `source-content.md`, not re-ingested
- [ ] Each output file saved with correct naming convention
- [ ] Summary table presented with status, file name, and word count for each format
- [ ] Failed formats noted in summary with reason
- [ ] Retry offered for any failed formats
- [ ] `processed-log.md` updated with this run's entry
- [ ] Brand voice reminder appended to each output file if setup was skipped
