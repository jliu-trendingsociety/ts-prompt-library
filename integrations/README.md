# Content Generation Integrations

> **Purpose:** n8n workflows, API templates, and integration patterns  
> **Updated:** December 2025

---

## Overview

This folder contains ready-to-deploy integrations for automating content generation using the Trending Society prompt system.

---

## Available Integrations

### n8n Workflows

| Workflow | Description | Complexity | File |
|----------|-------------|------------|------|
| **Blog Post Generator** | Fetch URL → AI Generate → Shopify Publish | Intermediate | `n8n-blog-post-generator.json` |
| **Social Media Distribution** | Blog Post → Generate Social Posts → Multi-Platform Post | Intermediate | `n8n-social-distribution.json` |
| **Content Verification** | Weekly check of logo URLs, links, and API versions | Simple | `n8n-verification-workflow.json` |
| **Full Distribution Pipeline** | URL → Blog + Social + Email + Images → Multi-Channel Publish | Advanced | `n8n-full-distribution.json` |

### API Templates

| Template | Description | File |
|----------|-------------|------|
| **Blog Post API** | RESTful API for blog generation | `api-blog-post.js` |
| **Social Post API** | RESTful API for social content | `api-social-post.js` |

---

## Quick Start

### 1. n8n Blog Post Generator

**What it does:**
- Accepts a source article URL
- Fetches and extracts content
- Generates SEO-optimized blog post using Editorial v5 prompt
- Publishes to Shopify
- Logs everything to Airtable

**Setup:**
1. Import `n8n-blog-post-generator.json` into n8n
2. Configure credentials:
   - OpenAI or Anthropic API key
   - Shopify API credentials
   - Airtable API key (for logging)
3. Set environment variables:
   - `BLOG_ID`: Your Shopify blog ID
   - `DEFAULT_AUTHOR`: Author name
4. Activate workflow

**Usage:**
```bash
# Trigger via webhook
curl -X POST https://your-n8n-instance.com/webhook/generate-blog \
  -H "Content-Type: application/json" \
  -d '{
    "source_url": "https://techcrunch.com/article",
    "content_type": "blog-post",
    "canonical_id": "article_009"
  }'
```

---

### 2. Social Media Distribution

**What it does:**
- Accepts blog post content or URL
- Generates platform-specific social posts (LinkedIn, Twitter, Instagram)
- Posts to multiple platforms
- Logs performance

**Setup:**
1. Import `n8n-social-distribution.json`
2. Configure social platform credentials
3. Set posting schedule
4. Activate workflow

---

### 3. Full Distribution Pipeline

**What it does:**
- Complete content package from source URL:
  - Blog post → Shopify
  - LinkedIn post → LinkedIn API
  - Twitter thread → Twitter API
  - Instagram caption → Buffer/Later
  - Newsletter → Email service
  - Image prompts → Midjourney

**Setup:**
1. Import `n8n-full-distribution.json`
2. Configure all service credentials
3. Set content strategy preferences
4. Activate workflow

---

## Integration Patterns

### Pattern 1: Webhook-Triggered Generation

```javascript
// n8n Webhook node receives request
{
  "source_url": "https://example.com/article",
  "content_type": "blog-post",
  "canonical_id": "article_009",
  "prompt_version": "v5",
  "publish": true
}

// n8n processes through:
// 1. Webhook Trigger
// 2. HTTP Request (fetch source)
// 3. Function (extract content)
// 4. AI Node (Claude/GPT)
// 5. Function (validate output)
// 6. Shopify Node (publish)
// 7. Airtable Node (log)
// 8. Respond to Webhook
```

### Pattern 2: Scheduled Batch Generation

```javascript
// n8n Cron node triggers daily
// → Fetch RSS feed or API
// → Filter new articles
// → For each article:
//   → Generate content
//   → Publish
//   → Log
// → Send summary email
```

### Pattern 3: Manual Trigger with Review

```javascript
// n8n Manual Trigger
// → Generate content
// → Save as draft
// → Send to Slack for review
// → Await approval (webhook)
// → If approved → Publish
// → If rejected → Store feedback
```

---

## Node Configurations

### AI Generation Node (Claude/GPT)

```json
{
  "model": "claude-sonnet-4",
  "temperature": 0.7,
  "max_tokens": 8000,
  "system": "[Load from editorial/v5/master-prompt.md]",
  "messages": [
    {
      "role": "user",
      "content": "Source article:\n\n{{ $json.article_content }}"
    }
  ],
  "response_format": {
    "type": "json_object"
  }
}
```

### Shopify Publish Node

```json
{
  "operation": "create",
  "resource": "article",
  "blogId": "{{ $env.BLOG_ID }}",
  "title": "{{ $json.blog_title }}",
  "body_html": "{{ $json.content_html }}",
  "author": "{{ $env.DEFAULT_AUTHOR }}",
  "tags": "{{ $json.tags }}",
  "published": true
}
```

### Airtable Logging Node

```json
{
  "operation": "create",
  "table": "content_logs",
  "fields": {
    "timestamp": "{{ $now }}",
    "canonical_id": "{{ $json.canonical_id }}",
    "content_type": "{{ $json.content_type }}",
    "model_used": "{{ $json.model }}",
    "tokens_total": "{{ $json.usage.total_tokens }}",
    "cost_usd": "{{ $json.cost }}",
    "result": "success"
  }
}
```

---

## Error Handling

### Pattern: Error Trigger Workflow

Every n8n workflow should have an Error Trigger:

```json
{
  "name": "Error Trigger",
  "type": "n8n-nodes-base.errorTrigger",
  "parameters": {},
  "connections": {
    "main": [
      [
        {
          "node": "Log Error",
          "type": "main",
          "index": 0
        }
      ]
    ]
  }
}
```

**Error handling workflow:**
1. Error Trigger catches failures
2. Log to Airtable with full context
3. Check error type (retryable vs fatal)
4. If retryable → Add to retry queue
5. If fatal → Send Slack alert
6. Store error for analysis

---

## Cost Monitoring

### Pattern: Budget Tracking Node

Add after every AI generation:

```javascript
// Function node: Calculate Cost
const model = $json.model;
const tokens = $json.usage.total_tokens;

const PRICING = {
  "claude-sonnet-4": 3.00 / 1_000_000,
  "gpt-4o-mini": 0.15 / 1_000_000,
  "gemini-pro": 1.25 / 1_000_000
};

const cost = tokens * (PRICING[model] || 0);

// Check daily budget
const today = new Date().toISOString().split('T')[0];
const dailySpend = await db.sum('content_logs', { date: today }, 'cost_usd');
const DAILY_BUDGET = 50.00;

if (dailySpend + cost > DAILY_BUDGET) {
  throw new Error(`Daily budget exceeded: $${dailySpend + cost} > $${DAILY_BUDGET}`);
}

return {
  ...input,
  cost,
  daily_spend: dailySpend + cost,
  budget_remaining: DAILY_BUDGET - (dailySpend + cost)
};
```

---

## Testing Workflows

### Development Mode

Set environment variable:
```bash
ENV=development
```

In workflows, use test credentials and draft mode:

```javascript
// Function node: Check Environment
const isDev = $env.ENV === 'development';

return {
  ...input,
  publish: !isDev,  // Don't publish in dev
  shopify_status: isDev ? 'draft' : 'published',
  log_prefix: isDev ? '[DEV]' : '[PROD]'
};
```

---

## Deployment Checklist

Before activating a workflow:

☐ All credentials configured  
☐ Environment variables set  
☐ Error Trigger workflow added  
☐ Logging nodes configured  
☐ Cost monitoring enabled  
☐ Budget alerts set up  
☐ Test run completed successfully  
☐ Idempotency keys implemented  
☐ Circuit breakers in place  
☐ Documentation updated  

---

_Built by Trending Society · Production-ready workflows_

