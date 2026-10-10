# RepurposeNow

**Turn one long-form piece into a week of content.**

Drop in a YouTube video, podcast, blog post, or transcript. Get back LinkedIn posts, X threads, an email newsletter, a blog post, short-form video scripts, and carousel slides, all written in your own brand voice. Free, MIT, nothing required to connect.

Works with Claude Code, Claude Cowork, and Claude desktop. Built by [Swayam Dhawan](https://github.com/swayamdhawan) at [Creatofy](https://creatofy.com).

## What you get

| Skill | Command | Output |
|---|---|---|
| Setup | `/setup-repurposer` | Choose where content is saved, your source folder, and default formats |
| Brand voice | `/setup-voice` | A short interview and writing-sample analysis saved as `brand-voice.md` |
| LinkedIn | `/repurpose-linkedin` | LinkedIn posts from the source |
| X / Twitter | `/repurpose-twitter` | A thread built from the source |
| Email | `/repurpose-email` | A newsletter, optionally as a Gmail draft |
| Blog | `/repurpose-blog` | A full blog post |
| Shorts | `/repurpose-shorts` | Short-form video scripts |
| Carousel | `/repurpose-carousel` | Slide-by-slide carousel copy |
| **All at once** | `/repurpose-all` | Every format in parallel, with a summary table. Can run on a schedule |

## How it works

1. **Setup once.** Run `/setup-repurposer` to pick an output folder, then `/setup-voice` so everything sounds like you.
2. **Give it a source.** A URL (YouTube, blog, podcast), a file, or pasted text.
3. **Pick a format** or run `/repurpose-all`. The source is read once, then one sub-agent per format runs in parallel.
4. **Get the files.** Saved to your local folder, Notion, or Google Drive. Every run is logged to `processed-log.md`.

If one format fails, the others still finish and you get a report of what worked.

## Example

```
/repurpose-all https://www.youtube.com/watch?v=...
```

```
Source ingested: "How I got 10k followers in 90 days" (14 min)

Format      Status    File
LinkedIn    done      linkedin-post.md
X thread    done      x-thread.md
Email       done      newsletter.md
Blog        done      blog-post.md
Shorts      done      shorts-scripts.md
Carousel    done      carousel.md
```

*(Illustrative output. Actual file names and layout depend on your setup.)*

## Install

### Claude Code (plugin)

```
/plugin marketplace add swayamdhawan/repurposenow
/plugin install repurposenow
```

### Claude Code (GitHub CLI, as plain skills)

```bash
gh repo clone swayamdhawan/repurposenow /tmp/repurposenow
mkdir -p ~/.claude/skills && cp -R /tmp/repurposenow/skills/* ~/.claude/skills/
```

### Claude Cowork and Claude desktop

Download the repo as a zip, then add it under **Settings > Plugins** (or **Customize > Plugins**):

```bash
gh repo clone swayamdhawan/repurposenow && cd repurposenow && zip -r ../repurposenow.zip . -x '.git/*'
```

Restart the app, then run `/setup-repurposer`.

## Optional connectors

Everything works with local files. If you have these connected, the plugin uses them automatically. See [`CONNECTORS.md`](CONNECTORS.md).

| Need | Default tool |
|---|---|
| Blog and podcast URLs | Firecrawl |
| YouTube transcripts | Apify |
| Saving output | Notion, Google Drive, or a local folder |
| Email drafts | Gmail |

## Repo layout

```
.claude-plugin/        plugin.json and marketplace.json
skills/                nine skills (setup, voice, six formats, repurpose-all)
shared/ingestion.md    shared source-reading module used by every format skill
CONNECTORS.md          optional connector mappings
LICENSE                MIT
```

Settings are saved to `~/.claude/plugins/repurposenow/` (`repurposer-config.md` and `brand-voice.md`).

## License

MIT. See [`LICENSE`](LICENSE) for copyright notices.
