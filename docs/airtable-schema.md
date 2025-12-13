# Airtable Data Layer Schema

> **Version:** 1.0  
> **Updated:** December 12, 2025  
> **Base Name:** Content Engine  
> **Status:** 🔲 Design Complete

---

## Overview

Airtable serves as the data layer for the Content Engine, providing:

- Input queue management
- Content storage and versioning
- Multi-tenant client management
- Enrichment tracking
- Platform connection management

---

## Base Structure

```
Content Engine (Base)
├── Requests         # Input queue
├── Articles         # Generated content
├── Enrichments      # Podcast, video, social outputs
├── Clients          # Multi-tenant management
├── Platform_Connections  # OAuth/API credentials
└── Config           # System settings
```

---

## Table: Requests

**Purpose:** Input queue for content generation requests

| Field              | Type             | Description                           |
| ------------------ | ---------------- | ------------------------------------- |
| `request_id`       | Auto Number      | Unique identifier                     |
| `source_url`       | URL              | Article URL to process                |
| `source_text`      | Long Text        | Or pasted content                     |
| `vertical`         | Single Select    | Tech, Sports, Finance, etc.           |
| `target_platform`  | Single Select    | Shopify, WordPress, etc.              |
| `client`           | Link to Clients  | Who requested                         |
| `status`           | Single Select    | Pending, Processing, Complete, Failed |
| `priority`         | Single Select    | Low, Normal, High, Urgent             |
| `options`          | Long Text (JSON) | Generation options                    |
| `created_at`       | Created Time     | Timestamp                             |
| `processed_at`     | Date/Time        | When completed                        |
| `n8n_execution_id` | Text             | For tracking                          |
| `error_message`    | Long Text        | If failed                             |

**Views:**

- Kanban by Status (drag-drop workflow)
- Pending Queue (filtered, sorted by priority)
- Failed Requests (for retry)

---

## Table: Articles

**Purpose:** Storage for generated content

| Field              | Type             | Description                |
| ------------------ | ---------------- | -------------------------- |
| `article_id`       | Auto Number      | Unique identifier          |
| `request`          | Link to Requests | Source request             |
| `title`            | Text             | Generated title            |
| `content_html`     | Long Text        | Full HTML content          |
| `excerpt`          | Long Text        | Summary                    |
| `seo_title`        | Text             | Page title                 |
| `meta_description` | Text             | Meta description           |
| `slug`             | Text             | URL handle                 |
| `tags`             | Multiple Select  | Generated tags             |
| `schema_json`      | Long Text        | JSON-LD schema             |
| `vertical`         | Single Select    | Content vertical           |
| `status`           | Single Select    | Draft, Approved, Published |
| `quality_score`    | Number (1-100)   | AI quality rating          |
| `word_count`       | Number           | Article length             |
| `published_url`    | URL              | Final published URL        |
| `published_at`     | Date/Time        | When published             |
| `author`           | Link to Authors  | Content author             |
| `client`           | Link to Clients  | Owner                      |

**Views:**

- Gallery with thumbnails
- Approval Queue (Draft status)
- By Vertical (grouped)
- By Client (filtered)

---

## Table: Enrichments

**Purpose:** Track podcast, video, social outputs

| Field              | Type             | Description                             |
| ------------------ | ---------------- | --------------------------------------- |
| `enrichment_id`    | Auto Number      | Unique identifier                       |
| `article`          | Link to Articles | Parent article                          |
| `type`             | Single Select    | Podcast, Video, Twitter, LinkedIn, etc. |
| `status`           | Single Select    | Pending, Processing, Complete, Failed   |
| `output_url`       | URL              | Generated asset URL                     |
| `output_content`   | Long Text        | Text content (for social)               |
| `platform_post_id` | Text             | If published to platform                |
| `duration`         | Number           | For audio/video (seconds)               |
| `created_at`       | Created Time     | Timestamp                               |
| `completed_at`     | Date/Time        | When finished                           |

**Views:**

- By Type (grouped)
- Pending Enrichments
- By Article (linked)

---

## Table: Clients

**Purpose:** Multi-tenant client management

| Field                | Type            | Description                        |
| -------------------- | --------------- | ---------------------------------- |
| `client_id`          | Auto Number     | Unique identifier                  |
| `name`               | Text            | Company name                       |
| `email`              | Email           | Primary contact                    |
| `plan`               | Single Select   | Starter, Growth, Scale, Enterprise |
| `credits_remaining`  | Number          | Usage tracking                     |
| `credits_used_month` | Number          | Monthly usage                      |
| `platforms`          | Multiple Select | Connected platforms                |
| `verticals`          | Multiple Select | Allowed verticals                  |
| `api_key`            | Text            | For API access                     |
| `created_at`         | Created Time    | Signup date                        |
| `status`             | Single Select   | Active, Paused, Churned            |

**Views:**

- All Clients
- By Plan (grouped)
- Active (filtered)
- Low Credits (formula filter)

---

## Table: Platform_Connections

**Purpose:** Store OAuth/API credentials

| Field           | Type            | Description               |
| --------------- | --------------- | ------------------------- |
| `connection_id` | Auto Number     | Unique identifier         |
| `client`        | Link to Clients | Owner                     |
| `platform`      | Single Select   | Shopify, WordPress, etc.  |
| `credentials`   | Long Text       | Encrypted credentials     |
| `site_url`      | URL             | Target site               |
| `blog_id`       | Text            | Platform-specific blog ID |
| `status`        | Single Select   | Active, Expired, Error    |
| `last_used`     | Date/Time       | Last successful use       |
| `created_at`    | Created Time    | When connected            |

**Views:**

- By Client (filtered)
- By Platform (grouped)
- Expired/Error (for maintenance)

---

## Table: Config

**Purpose:** System settings

| Field         | Type               | Description                   |
| ------------- | ------------------ | ----------------------------- |
| `key`         | Text               | Config key                    |
| `value`       | Long Text          | Config value                  |
| `type`        | Single Select      | string, number, json, boolean |
| `description` | Text               | What it does                  |
| `updated_at`  | Last Modified Time | When changed                  |

**Example Configs:**

- `default_vertical`: "tech"
- `openai_model`: "gpt-4-turbo"
- `rate_limit_requests_per_minute`: "30"
- `elevenlabs_default_voice`: "Rachel"

---

## Automations

### On New Request

1. Trigger: New record in Requests
2. Action: Call n8n webhook with request data
3. Update: Set status to "Processing"

### On Article Complete

1. Trigger: Status changed to "Complete"
2. Action: Create Enrichment records (if options enabled)
3. Action: Trigger enrichment webhooks

### On Low Credits

1. Trigger: credits_remaining < 5
2. Action: Send email notification to client

---

## Views Summary

| Table                | Key Views                            |
| -------------------- | ------------------------------------ |
| Requests             | Kanban, Pending Queue, Failed        |
| Articles             | Gallery, Approval Queue, By Vertical |
| Enrichments          | By Type, Pending                     |
| Clients              | By Plan, Active, Low Credits         |
| Platform_Connections | By Client, By Platform               |

---

## Integration with n8n

### Webhook Triggers

- `POST /webhooks/airtable/request` - New request
- `POST /webhooks/airtable/approved` - Article approved

### API Calls from n8n

- Create/update records via Airtable API
- Query by formula for filtering
- Batch operations for efficiency

---

_Schema designed by Trending Society_
