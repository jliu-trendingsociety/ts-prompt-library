# Content Engine API

> **Version:** 1.0 (Specification)  
> **Updated:** December 12, 2025  
> **Status:** 🔲 Planned

---

## Overview

The Content Engine API provides programmatic access to article generation, enrichment, and publishing capabilities.

---

## Base URL

```
Production: https://api.trendingsociety.com/v1
Staging: https://api-staging.trendingsociety.com/v1
```

---

## Authentication

### API Key (Header)
```
X-API-Key: your_api_key_here
```

### Bearer Token (OAuth)
```
Authorization: Bearer your_token_here
```

---

## Core Endpoints

### Articles

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/articles/generate` | Generate article from source |
| GET | `/articles/{id}` | Get article by ID |
| POST | `/articles/{id}/publish` | Publish to connected platform |
| POST | `/articles/bulk` | Batch generation |
| GET | `/articles` | List articles (paginated) |

### Enrichments

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/articles/{id}/enrich/podcast` | Generate podcast |
| POST | `/articles/{id}/enrich/video` | Generate video |
| POST | `/articles/{id}/enrich/social` | Generate social posts |
| GET | `/enrichments/{id}` | Get enrichment status |

### Platforms

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/platforms` | List connected platforms |
| POST | `/platforms/{platform}/connect` | Connect new platform |
| DELETE | `/platforms/{connection_id}` | Disconnect platform |

### Webhooks

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/webhooks` | Register webhook |
| GET | `/webhooks` | List webhooks |
| DELETE | `/webhooks/{id}` | Delete webhook |

---

## Generate Article

### Request

```bash
POST /v1/articles/generate
Content-Type: application/json
X-API-Key: your_api_key

{
  "source": {
    "type": "url",
    "value": "https://techcrunch.com/article..."
  },
  "vertical": "tech",
  "platform": "shopify",
  "options": {
    "generate_podcast": true,
    "generate_video": false,
    "generate_social": true
  }
}
```

### Response

```json
{
  "id": "art_123abc",
  "status": "processing",
  "estimated_completion": "2025-12-12T12:00:30Z",
  "webhook_url": "https://api.trendingsociety.com/v1/articles/art_123abc"
}
```

---

## Webhook Events

| Event | Description |
|-------|-------------|
| `article.generated` | Article generation complete |
| `article.published` | Article published to platform |
| `article.failed` | Generation or publish failed |
| `enrichment.complete` | Podcast/video/social ready |

### Webhook Payload

```json
{
  "event": "article.generated",
  "timestamp": "2025-12-12T12:00:30Z",
  "data": {
    "article_id": "art_123abc",
    "title": "...",
    "status": "complete",
    "url": "https://..."
  }
}
```

---

## Rate Limits

| Tier | Requests/min | Articles/mo |
|------|--------------|-------------|
| Starter | 10 | 25 |
| Growth | 30 | 100 |
| Scale | 60 | 300 |
| Enterprise | 120 | 1,000+ |

---

## Error Codes

| Code | Meaning |
|------|---------|
| 400 | Bad request (validation error) |
| 401 | Unauthorized (invalid API key) |
| 403 | Forbidden (insufficient permissions) |
| 404 | Resource not found |
| 429 | Rate limit exceeded |
| 500 | Internal server error |

---

## SDKs (Planned)

- JavaScript/TypeScript
- Python
- n8n Community Node

---

*API maintained by Trending Society*
