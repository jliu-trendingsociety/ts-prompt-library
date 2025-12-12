# n8n Workflow Blueprints

> **Version:** 1.0  
> **Updated:** December 12, 2025  
> **Status:** 🔲 Design Complete

---

## Overview

n8n serves as the backend orchestration layer, handling:

- Content generation pipeline
- Platform publishing
- Enrichment processing
- RSS ingestion and scheduling

---

## Workflow Architecture

```
┌─────────────────────────────────────────────────────────────────────────┐
│                          n8n WORKFLOW LAYER                             │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐   │
│  │   Article   │  │  Platform   │  │ Enrichment  │  │    RSS      │   │
│  │ Generation  │  │  Publishing │  │  Pipeline   │  │  Ingestion  │   │
│  │  Pipeline   │  │  Pipeline   │  │             │  │             │   │
│  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────┘   │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                        AIRTABLE DATA LAYER                              │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## Workflow 1: Article Generation Pipeline

### Trigger

- Airtable webhook (new Request record)
- Manual trigger for testing

### Flow

```
1. Webhook Trigger
   ↓
2. Get Request Data (Airtable)
   ↓
3. Fetch Source Content
   ├─ URL → HTTP Request + Readability parse
   └─ Text → Direct use
   ↓
4. Detect Vertical (AI)
   ↓
5. Load Prompt Override (if vertical-specific)
   ↓
6. Generate Article (OpenAI/Claude)
   ├─ System: Master Prompt v5.2
   ├─ User: Source content + vertical context
   └─ Output: 10 structured sections
   ↓
7. Parse Output
   ↓
8. Generate Schema JSON
   ↓
9. Quality Gate Check (AI validation)
   ↓
10. Save to Airtable (Articles table)
    ↓
11. Update Request Status
    ↓
12. Trigger Enrichments (if enabled)
```

### Nodes

| Node         | Type      | Purpose                  |
| ------------ | --------- | ------------------------ |
| Webhook      | Trigger   | Receive Airtable webhook |
| Airtable     | Read      | Get request details      |
| HTTP Request | Action    | Fetch source URL         |
| Readability  | Transform | Extract article content  |
| Switch       | Logic     | Route by vertical        |
| OpenAI       | AI        | Generate article         |
| Code         | Transform | Parse AI output          |
| Airtable     | Write     | Save article             |
| HTTP Request | Action    | Trigger enrichments      |

---

## Workflow 2: Platform Publishing

### Trigger

- Airtable webhook (Article status → "Approved")
- Manual trigger

### Flow

```
1. Webhook Trigger
   ↓
2. Get Article Data (Airtable)
   ↓
3. Get Platform Connection (Airtable)
   ↓
4. Switch by Platform
   ├─ Shopify → Format for Shopify API
   ├─ WordPress → Format for WP REST API
   ├─ Webflow → Format for Webflow CMS API
   └─ Other → Generic HTML output
   ↓
5. Inject Schema JSON
   ↓
6. Publish via Platform API
   ↓
7. Update Article (published_url, published_at)
   ↓
8. Send Webhook Notification
```

### Platform Nodes

| Platform  | Node Type    | Endpoint                                      |
| --------- | ------------ | --------------------------------------------- |
| Shopify   | HTTP Request | `/admin/api/2024-10/blogs/{id}/articles.json` |
| WordPress | HTTP Request | `/wp-json/wp/v2/posts`                        |
| Webflow   | HTTP Request | `/v2/collections/{id}/items`                  |

---

## Workflow 3: Enrichment Pipeline

### Trigger

- HTTP webhook from Article Generation
- Scheduled batch processing

### Flow

```
1. Webhook Trigger (article_id, enrichment_types)
   ↓
2. Get Article Data (Airtable)
   ↓
3. Switch by Enrichment Type
   │
   ├─ Podcast
   │  ├─ Generate script from article
   │  ├─ Call ElevenLabs TTS API
   │  ├─ Upload to storage (S3/Cloudinary)
   │  └─ Save to Enrichments table
   │
   ├─ Video
   │  ├─ Generate video script
   │  ├─ Call HeyGen/Decart API
   │  ├─ Wait for completion
   │  └─ Save to Enrichments table
   │
   └─ Social
      ├─ Generate Twitter thread
      ├─ Generate LinkedIn post
      ├─ Generate Instagram caption
      └─ Save to Enrichments table
   ↓
4. Update Article (enrichments complete)
```

### Enrichment APIs

| Type    | Provider   | Endpoint                                      |
| ------- | ---------- | --------------------------------------------- |
| Podcast | ElevenLabs | `https://api.elevenlabs.io/v1/text-to-speech` |
| Video   | HeyGen     | `https://api.heygen.com/v1/video/generate`    |
| Video   | Decart     | `https://api.decart.ai/v1/generate`           |

---

## Workflow 4: RSS Ingestion

### Trigger

- Schedule trigger (every 15 minutes)

### Flow

```
1. Schedule Trigger
   ↓
2. Get Innoreader Feeds (HTTP Request)
   ↓
3. Parse RSS/Atom Feed
   ↓
4. Loop through items
   ↓
5. Check for duplicates (Airtable query)
   ↓
6. Filter by vertical rules
   ↓
7. Create Request records (Airtable)
   ↓
8. Log ingestion stats
```

### Innoreader Integration

```
GET https://www.innoreader.com/reader/api/0/stream/contents/{feed_id}
Authorization: Bearer {api_token}
```

---

## Environment Variables

| Variable               | Description                    |
| ---------------------- | ------------------------------ |
| `AIRTABLE_API_KEY`     | Airtable personal access token |
| `AIRTABLE_BASE_ID`     | Content Engine base ID         |
| `OPENAI_API_KEY`       | OpenAI API key                 |
| `ANTHROPIC_API_KEY`    | Claude API key                 |
| `ELEVENLABS_API_KEY`   | ElevenLabs API key             |
| `HEYGEN_API_KEY`       | HeyGen API key                 |
| `SHOPIFY_ACCESS_TOKEN` | Shopify Admin API token        |
| `INNOREADER_TOKEN`     | Innoreader API token           |

---

## Error Handling

### Retry Strategy

- Max retries: 3
- Backoff: Exponential (1s, 2s, 4s)
- On final failure: Update status to "Failed", log error

### Alert Channels

- Slack webhook for critical failures
- Email for daily summary

---

## Monitoring

### Execution Logs

- n8n execution history
- Custom logging to Airtable (optional)

### Metrics to Track

- Articles generated per day
- Average generation time
- Failure rate by step
- API costs per article

---

_Workflows designed by Trending Society_
