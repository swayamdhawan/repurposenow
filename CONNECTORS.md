# RepurposeNow — Connector Categories

Skills in this plugin reference connectors by category using `~~` placeholders. This makes the plugin portable across different tool setups.

## Connector Mappings

| Category | Placeholder | Default Tool | Used By |
|----------|-------------|-------------|---------|
| Web scraper | `~~web scraper` | Firecrawl | Ingestion (blog/podcast URLs) |
| Video transcript | `~~video transcript scraper` | Apify (`topaz_sharingan/Youtube-Transcript-Scraper-1`) | Ingestion (YouTube URLs) |
| Document storage | `~~document storage` | Notion, Google Drive | All format skills (output delivery) |
| Email | `~~email` | Gmail | `/repurpose-email` (draft creation) |

## No Connectors Required

All connectors are optional. The plugin works fully with local files and folder-based output. When a connector is unavailable and needed, skills offer manual paste or file-based fallbacks.

## Configuring Connectors

If you have connectors installed in Claude Cowork (Settings → Connectors), the plugin will use them automatically when relevant. No additional configuration needed.
