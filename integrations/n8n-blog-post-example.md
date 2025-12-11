# n8n Blog Post Generator Workflow

> **Purpose:** Complete example of automated blog post generation workflow  
> **Complexity:** Intermediate  
> **Estimated setup time:** 30 minutes

---

## Workflow Overview

```
[Webhook] → [Fetch URL] → [Extract Content] → [Route Model] → [Load Prompt] → [AI Generate] →
[Quality Check] → [Shopify Publish] → [Log to Airtable] → [Respond]
                                          ↓
                                    [Error Trigger] → [Log Error] → [Alert Slack]
```

**Note:** This workflow uses only n8n-compatible patterns. Function nodes use only globally available modules (`crypto`, `url`, `querystring`). External modules like `@mozilla/readability`, `jsdom`, or `fs` are not available in Function nodes.

---

## Node Breakdown

### 1. Webhook Trigger

**Node type:** `n8n-nodes-base.webhook`

**Configuration:**

```json
{
  "httpMethod": "POST",
  "path": "generate-blog",
  "responseMode": "responseNode",
  "authentication": "headerAuth"
}
```

**Expected payload:**

```json
{
  "source_url": "https://techcrunch.com/2025/12/06/ai-news",
  "content_type": "blog-post",
  "canonical_id": "article_009",
  "prompt_version": "v5-lite",
  "publish": true
}
```

---

### 2. Fetch Source Content

**Node type:** `n8n-nodes-base.httpRequest`

**Configuration:**

```json
{
  "method": "GET",
  "url": "={{ $json.body.source_url }}",
  "timeout": 10000,
  "options": {
    "redirect": {
      "followRedirects": true
    }
  }
}
```

**Output:**

- `html`: Raw HTML of the page
- `status_code`: HTTP status

---

### 3. Extract Content

**Node type:** `n8n-nodes-base.httpRequest`

**Configuration:**

```json
{
  "method": "POST",
  "url": "https://api.jina.ai/v1/extract",
  "authentication": "headerAuth",
  "headers": {
    "Authorization": "Bearer {{ $env.JINA_API_KEY }}",
    "Content-Type": "application/json"
  },
  "body": {
    "url": "={{ $json.body.source_url }}"
  }
}
```

**Alternative: Function Node (Simple Extraction)**

If using simple HTML parsing without external libraries:

```javascript
// Simple HTML to text extraction (no external modules)
const html = $input.item.json.html;

// Extract title from <title> or <h1>
const titleMatch =
  html.match(/<title>([^<]+)<\/title>/) || html.match(/<h1[^>]*>([^<]+)<\/h1>/);
const title = titleMatch ? titleMatch[1].trim() : 'Untitled';

// Extract main content (simple approach)
// Remove scripts, styles, nav, footer
let content = html
  .replace(/<script\b[^<]*(?:(?!<\/script>)<[^<]*)*<\/script>/gi, '')
  .replace(/<style\b[^<]*(?:(?!<\/style>)<[^<]*)*<\/style>/gi, '')
  .replace(/<nav\b[^<]*(?:(?!<\/nav>)<[^<]*)*<\/nav>/gi, '')
  .replace(/<footer\b[^<]*(?:(?!<\/footer>)<[^<]*)*<\/footer>/gi, '')
  .replace(/<[^>]+>/g, ' ')
  .replace(/\s+/g, ' ')
  .trim();

// Generate idempotency key (crypto is globally available)
const idempotencyKey = crypto
  .createHash('sha256')
  .update(
    JSON.stringify({
      source_url: $json.body.source_url,
      date: new Date().toISOString().split('T')[0],
    })
  )
  .digest('hex');

return {
  source_url: $json.body.source_url,
  canonical_id: $json.body.canonical_id,
  content_type: $json.body.content_type,
  prompt_version: $json.body.prompt_version || 'v5-lite',
  publish: $json.body.publish !== false,

  // Extracted content
  title: title,
  excerpt: content.substring(0, 200),
  content_text: content,
  content_length: content.length,

  // Metadata
  idempotency_key: idempotencyKey,
  extracted_at: new Date().toISOString(),
};
```

**⚠️ Important:** n8n Function nodes **cannot use `require()`** for external modules. For advanced HTML parsing:

- **Option 1 (Recommended):** Use external service like Jina AI, Diffbot, or similar
- **Option 2:** Use simple regex extraction (shown above)
- **Option 3:** Use n8n Code node with external dependencies enabled
- **Never use:** `require('@mozilla/readability')`, `require('jsdom')`, etc. in Function nodes

See `.cursor/rules/n8n-function-node-reference.mdc` for available modules.

---

### 4. Check Existing

**Node type:** `n8n-nodes-base.airtable`

**Configuration:**

```json
{
  "operation": "list",
  "table": "content_index",
  "filterByFormula": "idempotency_key = '{{ $json.idempotency_key }}'",
  "maxRecords": 1
}
```

**Split:** If exists → Return existing, else → Continue

---

### 5. Route Model

**Node type:** `n8n-nodes-base.function`

**Code:**

```javascript
// Select model based on content_type and prompt_version
const MODEL_ROUTING = {
  'blog-post|v5': 'claude-sonnet-4',
  'blog-post|v5-lite': 'gpt-4o-mini',
  'social-single': 'gemini-flash-2.0',
  'email-single': 'gemini-pro-1.5',
};

const key = `${$json.content_type}|${$json.prompt_version}`;
const model = MODEL_ROUTING[key] || 'gpt-4o-mini';

// Prompt templates should be stored in Airtable or loaded via HTTP Request node
// n8n Function nodes cannot use fs.readFileSync()
// Option 1: Pre-load from Airtable
// Option 2: Use HTTP Request node to fetch from GitHub/CDN
// Option 3: Embed prompt in workflow as variable

// For this example, we'll reference prompt location
const promptReference = `editorial/${$json.prompt_version}/master-prompt.md`;

return {
  ...$json,
  model,
  prompt_reference: promptReference,
  max_tokens: model === 'claude-sonnet-4' ? 8000 : 4000,
};
```

---

### 5.5. Load Prompt Template

**Node type:** `n8n-nodes-base.airtable`

Load prompt template from Airtable (recommended) or use HTTP Request to fetch from GitHub.

**Configuration (Airtable):**

```json
{
  "operation": "get",
  "table": "PromptTemplates",
  "id": "={{ $json.prompt_reference }}"
}
```

**Alternative: HTTP Request from GitHub:**

```json
{
  "method": "GET",
  "url": "https://raw.githubusercontent.com/your-org/prompts/main/={{ $json.prompt_reference }}"
}
```

**Alternative: Embedded in Workflow Variable:**
Store prompt as workflow variable `$workflow.staticData.prompts[version]`

---

### 6. AI Generate

**Node type:** `n8n-nodes-base.openAi` (or Anthropic)

**Configuration:**

```json
{
  "resource": "chat",
  "operation": "create",
  "model": "={{ $('Route Model').item.json.model }}",
  "messages": {
    "values": [
      {
        "role": "system",
        "content": "={{ $('Load Prompt Template').item.json.prompt_content }}"
      },
      {
        "role": "user",
        "content": "Source article:\n\nTitle: {{ $('Extract Content').item.json.title }}\nURL: {{ $('Extract Content').item.json.source_url }}\n\n{{ $('Extract Content').item.json.content_text }}"
      }
    ]
  },
  "options": {
    "temperature": 0.7,
    "maxTokens": "={{ $json.max_tokens }}",
    "response_format": {
      "type": "json_object"
    }
  }
}
```

---

### 7. Parse & Validate Output

**Node type:** `n8n-nodes-base.function`

**Code:**

```javascript
// Parse AI response
const response = JSON.parse($json.choices[0].message.content);

// Validate required fields
const required = [
  'blog_title',
  'content_html',
  'excerpt',
  'page_title',
  'meta_description',
  'url_handle',
  'tags',
];

for (const field of required) {
  if (!response[field]) {
    throw new Error(`Missing required field: ${field}`);
  }
}

// Quality checks
const qualityGates = {
  has_source_badge: response.content_html.includes('source-badge'),
  has_internal_links:
    (response.content_html.match(/href="\/blogs\//g) || []).length >= 2,
  no_raw_urls: !response.content_html.match(/https?:\/\/[^\s<]+(?=\s|<|$)/),
  no_em_dashes: !response.content_html.includes('—'),
  has_faq_schema: response.content_html.includes(
    'itemtype="https://schema.org/FAQPage"'
  ),
};

const failed = Object.entries(qualityGates)
  .filter(([k, v]) => !v)
  .map(([k]) => k);

if (failed.length > 0) {
  console.log(`Quality gates failed: ${failed.join(', ')}`);
  // Don't throw, but log warning
}

// Calculate cost
const tokens = $json.usage.total_tokens;
const costPerMillion =
  {
    'claude-sonnet-4': 3.0,
    'gpt-4o-mini': 0.15,
    'gemini-pro-1.5': 1.25,
  }[$input.item.json.model] || 0.15;
const cost = (tokens / 1_000_000) * costPerMillion;

return {
  ...$input.item.json,
  ...response,

  // Metadata
  tokens_total: tokens,
  cost_usd: cost,
  quality_gates: qualityGates,
  quality_gates_failed: failed,
  generated_at: new Date().toISOString(),
};
```

---

### 8. Shopify Publish

**Node type:** `n8n-nodes-base.shopify`

**Configuration:**

```json
{
  "resource": "article",
  "operation": "create",
  "blogId": "={{ $env.SHOPIFY_BLOG_ID }}",
  "title": "={{ $json.blog_title }}",
  "bodyHtml": "={{ $json.content_html }}",
  "author": "={{ $env.DEFAULT_AUTHOR }}",
  "tags": "={{ $json.tags }}",
  "metafields": [
    {
      "namespace": "custom",
      "key": "canonical_id",
      "value": "={{ $json.canonical_id }}",
      "type": "single_line_text_field"
    }
  ],
  "published": "={{ $json.publish }}"
}
```

---

### 9. Log to Airtable

**Node type:** `n8n-nodes-base.airtable`

**Configuration:**

```json
{
  "operation": "create",
  "table": "content_logs",
  "fields": {
    "timestamp": "={{ $now }}",
    "date": "={{ $now.toFormat('yyyy-MM-dd') }}",
    "workflow_id": "blog-post-generator",
    "execution_id": "={{ $execution.id }}",
    "canonical_id": "={{ $json.canonical_id }}",
    "content_type": "={{ $json.content_type }}",
    "action": "ai_generation",
    "result": "success",
    "model": "={{ $json.model }}",
    "tokens_total": "={{ $json.tokens_total }}",
    "cost_usd": "={{ $json.cost_usd }}",
    "quality_score": "={{ 1 - ($json.quality_gates_failed.length / Object.keys($json.quality_gates).length) }}",
    "shopify_article_id": "={{ $json.id }}",
    "url": "={{ $json.url }}"
  }
}
```

---

### 10. Respond to Webhook

**Node type:** `n8n-nodes-base.respondToWebhook`

**Configuration:**

```json
{
  "respondWith": "json",
  "responseBody": "={{ JSON.stringify({
    success: true,
    canonical_id: $json.canonical_id,
    article_id: $json.id,
    url: $json.url,
    cost_usd: $json.cost_usd,
    tokens_total: $json.tokens_total
  }) }}"
}
```

---

### Error Trigger Workflow

**Node type:** `n8n-nodes-base.errorTrigger`

Connected to:

1. **Log Error (Airtable)**

```json
{
  "operation": "create",
  "table": "content_logs",
  "fields": {
    "timestamp": "={{ $now }}",
    "workflow_id": "blog-post-generator",
    "execution_id": "={{ $execution.id }}",
    "action": "api_error",
    "result": "failure",
    "error_message": "={{ $json.error.message }}",
    "error_stack": "={{ $json.error.stack }}"
  }
}
```

2. **Alert Slack**

```json
{
  "channel": "#content-alerts",
  "text": "❌ Blog post generation failed",
  "blocks": [
    {
      "type": "section",
      "text": {
        "type": "mrkdwn",
        "text": "*Error:* {{ $json.error.message }}\n*Execution:* {{ $execution.id }}\n*Source:* {{ $('Webhook').first().json.body.source_url }}"
      }
    }
  ]
}
```

---

## Environment Variables

Set these in n8n Settings → Variables:

```bash
SHOPIFY_BLOG_ID=123456789
DEFAULT_AUTHOR=Jeffrey Liu
SHOPIFY_API_KEY=shpat_xxxxx
SHOPIFY_SHOP_NAME=trending-society
AIRTABLE_API_KEY=keyXXXXX
AIRTABLE_BASE_ID=appXXXXX
SLACK_WEBHOOK_URL=https://hooks.slack.com/services/XXX
DAILY_BUDGET_USD=50.00
```

---

## Testing

### 1. Test with Sample Article

```bash
curl -X POST https://your-n8n.com/webhook/generate-blog \
  -H "Content-Type: application/json" \
  -d '{
    "source_url": "https://techcrunch.com/2025/12/06/test-article",
    "content_type": "blog-post",
    "canonical_id": "test_001",
    "prompt_version": "v5-lite",
    "publish": false
  }'
```

### 2. Check Draft in Shopify

Verify the article was created as draft.

### 3. Review Logs in Airtable

Check content_logs table for entry.

### 4. Test Error Handling

Send invalid URL, verify error is logged and alert sent.

---

## Monitoring

### Daily Summary (Separate Workflow)

Run daily at 9am to send summary:

```javascript
// Get yesterday's logs
const yesterday = new Date();
yesterday.setDate(yesterday.getDate() - 1);
const logs = await airtable.query('content_logs', {
  date: yesterday.toISOString().split('T')[0],
});

// Calculate metrics
const metrics = {
  total_generations: logs.length,
  successes: logs.filter((l) => l.result === 'success').length,
  failures: logs.filter((l) => l.result === 'failure').length,
  total_cost: logs.reduce((sum, l) => sum + (l.cost_usd || 0), 0),
  avg_quality:
    logs.reduce((sum, l) => sum + (l.quality_score || 0), 0) / logs.length,
};

// Send to Slack
await slack.post({
  channel: '#content-reports',
  text: `📊 Daily Content Generation Report - ${yesterday.toDateString()}`,
  blocks: [
    {
      type: 'section',
      fields: [
        { type: 'mrkdwn', text: `*Total:* ${metrics.total_generations}` },
        { type: 'mrkdwn', text: `*Success:* ${metrics.successes}` },
        { type: 'mrkdwn', text: `*Failed:* ${metrics.failures}` },
        { type: 'mrkdwn', text: `*Cost:* $${metrics.total_cost.toFixed(2)}` },
        {
          type: 'mrkdwn',
          text: `*Avg Quality:* ${(metrics.avg_quality * 100).toFixed(1)}%`,
        },
      ],
    },
  ],
});
```

---

_Built by Trending Society · Ready to deploy_
