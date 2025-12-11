# Structured Logging Schema for Content Generation

> **Purpose:** Queryable logs at scale for content generation workflows  
> **Rule Alignment:** Core Rule 8 - Structured Logging  
> **Updated:** December 2025

---

## Philosophy

**Queryable logs at scale.**

When something breaks at 3 AM, you need to find it fast, understand why, and know exactly what to fix. Structured logs make this possible.

Every content generation event should produce a standardized log entry that can be:
- Queried by canonical_id
- Filtered by content_type
- Aggregated by cost
- Analyzed for quality trends
- Debugged in production

---

## Core Log Schema

### Required Fields (Every Log Entry)

```javascript
{
  // Timestamps
  "timestamp": "2025-12-06T19:23:45.123Z",         // ISO 8601
  "date": "2025-12-06",                            // For daily aggregation
  
  // Execution Context
  "workflow_id": "content-generation-blog",        // Which workflow/function
  "execution_id": "exec_abc123xyz",                // Unique execution ID
  "node": "AI Generate Blog Post",                 // Specific node/step
  
  // Content Identity
  "canonical_id": "article_009",                   // Immutable content ID
  "content_type": "blog-post",                     // See content types below
  
  // Action & Result
  "action": "ai_generation",                       // What happened
  "result": "success",                             // success|failure|partial
  "duration_ms": 12340,                            // Execution time
  
  // Metadata
  "metadata": {                                    // Flexible object
    "source_url": "https://example.com/article",
    "word_count": 1423
  }
}
```

---

## Content Type Taxonomy

Standardized content_type values:

```javascript
const CONTENT_TYPES = {
  // Editorial
  "blog-post": "Full blog article",
  "blog-post-lite": "Simplified blog post",
  "news-brief": "Short news update",
  
  // Social
  "social-linkedin": "LinkedIn post",
  "social-twitter": "Twitter/X post",
  "social-thread": "Twitter/X thread",
  "social-instagram": "Instagram caption",
  "social-facebook": "Facebook post",
  "social-threads": "Threads post",
  
  // Email
  "email-single": "Single email",
  "email-sequence": "Email sequence",
  "email-newsletter": "Newsletter",
  
  // Media
  "image-prompt": "AI image generation prompt",
  "video-script": "Video script",
  
  // Ads
  "ads-meta": "Meta (FB/IG) ads",
  "ads-google": "Google ads",
  "ads-linkedin": "LinkedIn ads",
  
  // Distribution
  "full-distribution": "Complete content package"
};
```

---

## Action Taxonomy

Standardized action values:

```javascript
const ACTIONS = {
  // Generation
  "ai_generation": "AI model invoked for content",
  "prompt_loaded": "Prompt template loaded",
  "content_extracted": "Source content extracted",
  
  // Processing
  "html_to_markdown": "HTML converted to Markdown",
  "quality_gate_check": "Quality validation run",
  "link_audit": "Internal/external links checked",
  
  // Publishing
  "shopify_publish": "Published to Shopify",
  "social_post": "Posted to social platform",
  "email_send": "Email sent via ESP",
  
  // Analysis
  "cost_calculated": "Token cost computed",
  "performance_tracked": "Engagement metrics logged",
  
  // Errors
  "api_error": "External API failure",
  "validation_failed": "Quality gate failed",
  "retry_attempted": "Automatic retry triggered"
};
```

---

## Result Taxonomy

```javascript
const RESULTS = {
  "success": "Operation completed successfully",
  "failure": "Operation failed completely",
  "partial": "Operation partially succeeded (some items failed)",
  "retry": "Operation will be retried",
  "skipped": "Operation skipped (condition not met)"
};
```

---

## Extended Schemas by Event Type

### 1. AI Generation Event

```javascript
{
  // Core fields (always included)
  "timestamp": "2025-12-06T19:23:45.123Z",
  "workflow_id": "content-generation-blog",
  "execution_id": "exec_abc123xyz",
  "node": "AI Generate Blog Post",
  "canonical_id": "article_009",
  "content_type": "blog-post",
  "action": "ai_generation",
  "result": "success",
  "duration_ms": 12340,
  
  // AI-specific fields
  "ai": {
    "model": "claude-sonnet-4",
    "prompt_version": "v5",
    "tokens_prompt": 3842,
    "tokens_output": 4123,
    "tokens_total": 7965,
    "cost_usd": 0.0239,
    "temperature": 0.7,
    "max_tokens": 8000
  },
  
  // Quality metrics
  "quality": {
    "score": 0.92,                           // 0-1 scale
    "gates_passed": ["seo", "citations", "links"],
    "gates_failed": [],
    "word_count": 1423,
    "readability_grade": 9.2
  },
  
  // Source info
  "source": {
    "url": "https://example.com/article",
    "domain": "example.com",
    "extracted_length": 5234
  }
}
```

### 2. Quality Gate Check Event

```javascript
{
  // Core fields
  "timestamp": "2025-12-06T19:23:47.456Z",
  "workflow_id": "content-generation-blog",
  "execution_id": "exec_abc123xyz",
  "node": "Quality Gate Check",
  "canonical_id": "article_009",
  "content_type": "blog-post",
  "action": "quality_gate_check",
  "result": "success",
  "duration_ms": 234,
  
  // Quality gate details
  "quality_gate": {
    "checks_run": 11,
    "checks_passed": 11,
    "checks_failed": 0,
    "details": {
      "primary_keyword": true,
      "source_cited": true,
      "internal_links": true,
      "external_links": true,
      "no_raw_urls": true,
      "entity_relationships": true,
      "faq_schema": true,
      "no_fluff": true,
      "no_em_dashes": true,
      "active_voice": true,
      "tags_valid": true
    }
  }
}
```

### 3. Publishing Event

```javascript
{
  // Core fields
  "timestamp": "2025-12-06T19:24:00.789Z",
  "workflow_id": "content-generation-blog",
  "execution_id": "exec_abc123xyz",
  "node": "Publish to Shopify",
  "canonical_id": "article_009",
  "content_type": "blog-post",
  "action": "shopify_publish",
  "result": "success",
  "duration_ms": 1234,
  
  // Publishing details
  "publishing": {
    "platform": "shopify",
    "blog_id": "123456789",
    "article_id": "987654321",
    "url": "/blogs/news/article-slug",
    "status": "published",                // draft|published|scheduled
    "published_at": "2025-12-06T19:24:00Z"
  }
}
```

### 4. Error Event

```javascript
{
  // Core fields
  "timestamp": "2025-12-06T19:23:55.678Z",
  "workflow_id": "content-generation-blog",
  "execution_id": "exec_abc123xyz",
  "node": "Fetch Source Content",
  "canonical_id": "article_009",
  "content_type": "blog-post",
  "action": "api_error",
  "result": "failure",
  "duration_ms": 5004,
  
  // Error details
  "error": {
    "type": "TimeoutError",
    "message": "Request timeout after 5000ms",
    "code": "ETIMEDOUT",
    "status_code": null,
    "retryable": true,
    "retry_attempt": 1,
    "max_retries": 3
  },
  
  // Context
  "context": {
    "source_url": "https://example.com/slow-page",
    "timeout_ms": 5000
  }
}
```

### 5. Cost Tracking Event

```javascript
{
  // Core fields
  "timestamp": "2025-12-06T19:24:01.000Z",
  "workflow_id": "content-generation-blog",
  "execution_id": "exec_abc123xyz",
  "node": "Cost Aggregation",
  "canonical_id": "article_009",
  "content_type": "blog-post",
  "action": "cost_calculated",
  "result": "success",
  "duration_ms": 5,
  
  // Cost breakdown
  "cost": {
    "total_usd": 0.0289,
    "breakdown": {
      "ai_generation": 0.0239,
      "content_extraction": 0.0025,
      "image_generation": 0.0025
    },
    "tokens_total": 7965,
    "budget_allocated": 0.050,
    "budget_remaining": 0.0211,
    "under_budget": true
  }
}
```

---

## Aggregation Queries

### Daily Cost Report

```sql
SELECT 
  date,
  content_type,
  COUNT(*) as generations,
  SUM(ai.cost_usd) as total_cost,
  AVG(ai.cost_usd) as avg_cost,
  AVG(quality.score) as avg_quality
FROM content_logs
WHERE action = 'ai_generation'
  AND result = 'success'
  AND date >= '2025-12-01'
GROUP BY date, content_type
ORDER BY date DESC, total_cost DESC;
```

### Quality Trend Analysis

```sql
SELECT 
  content_type,
  AVG(quality.score) as avg_quality,
  AVG(quality.word_count) as avg_words,
  AVG(duration_ms) as avg_duration_ms
FROM content_logs
WHERE action = 'ai_generation'
  AND result = 'success'
  AND timestamp >= NOW() - INTERVAL '30 days'
GROUP BY content_type;
```

### Error Rate by Model

```sql
SELECT 
  ai.model,
  COUNT(*) as total_attempts,
  SUM(CASE WHEN result = 'failure' THEN 1 ELSE 0 END) as failures,
  ROUND(100.0 * SUM(CASE WHEN result = 'failure' THEN 1 ELSE 0 END) / COUNT(*), 2) as failure_rate_pct
FROM content_logs
WHERE action = 'ai_generation'
  AND timestamp >= NOW() - INTERVAL '7 days'
GROUP BY ai.model
ORDER BY failure_rate_pct DESC;
```

### Content Performance by Canonical ID

```sql
SELECT 
  canonical_id,
  content_type,
  ai.cost_usd,
  quality.score,
  quality.word_count,
  publishing.url
FROM content_logs
WHERE canonical_id = 'article_009'
  AND action IN ('ai_generation', 'shopify_publish')
ORDER BY timestamp DESC;
```

---

## Storage Options

### Option 1: Airtable (Recommended for Small-Medium Volume)

**Pros:**
- Easy to set up
- Queryable via API
- Visual interface for debugging
- Good for < 10K logs/month

**Schema:**
```javascript
// Airtable Table: "Content Logs"
{
  "record_id": "recXYZ123",
  "timestamp": "2025-12-06T19:23:45.123Z",
  "workflow_id": "content-generation-blog",
  "execution_id": "exec_abc123xyz",
  "canonical_id": "article_009",
  "content_type": "blog-post",
  "action": "ai_generation",
  "result": "success",
  "duration_ms": 12340,
  "ai_model": "claude-sonnet-4",
  "tokens_total": 7965,
  "cost_usd": 0.0239,
  "quality_score": 0.92,
  "metadata": "{...}"  // JSON string
}
```

### Option 2: PostgreSQL (Recommended for High Volume)

**Pros:**
- True SQL queries
- Better for > 10K logs/month
- JSONB support for flexible metadata
- Time-series optimizations

**Schema:**
```sql
CREATE TABLE content_logs (
  id SERIAL PRIMARY KEY,
  timestamp TIMESTAMP WITH TIME ZONE NOT NULL,
  date DATE NOT NULL,
  workflow_id VARCHAR(100) NOT NULL,
  execution_id VARCHAR(100) NOT NULL,
  node VARCHAR(200),
  canonical_id VARCHAR(50),
  content_type VARCHAR(50) NOT NULL,
  action VARCHAR(50) NOT NULL,
  result VARCHAR(20) NOT NULL,
  duration_ms INTEGER,
  metadata JSONB,
  
  -- Indexes for common queries
  INDEX idx_timestamp (timestamp),
  INDEX idx_date (date),
  INDEX idx_canonical_id (canonical_id),
  INDEX idx_content_type (content_type),
  INDEX idx_action (action),
  INDEX idx_result (result)
);
```

### Option 3: File-Based JSON Lines (Simplest)

**Pros:**
- No database setup
- Easy to process with scripts
- Can migrate to DB later

**Format:**
```json
{"timestamp":"2025-12-06T19:23:45.123Z","workflow_id":"content-generation-blog","canonical_id":"article_009","action":"ai_generation","result":"success","duration_ms":12340}
{"timestamp":"2025-12-06T19:23:47.456Z","workflow_id":"content-generation-blog","canonical_id":"article_009","action":"quality_gate_check","result":"success","duration_ms":234}
```

---

## Implementation Examples

### n8n Logging Node

```javascript
// In n8n Function node after AI generation
const log = {
  timestamp: new Date().toISOString(),
  date: new Date().toISOString().split('T')[0],
  workflow_id: $workflow.id,
  execution_id: $execution.id,
  node: $node.name,
  canonical_id: $json.canonical_id || `article_${Date.now()}`,
  content_type: $json.content_type,
  action: "ai_generation",
  result: "success",
  duration_ms: $json.duration,
  ai: {
    model: $json.model,
    prompt_version: "v5",
    tokens_total: $json.usage.total_tokens,
    cost_usd: calculateCost($json.model, $json.usage.total_tokens)
  },
  quality: {
    score: $json.quality_score,
    word_count: $json.word_count
  }
};

// Store in Airtable
return log;
```

### JavaScript Logging Utility

```javascript
class ContentLogger {
  constructor(storage) {
    this.storage = storage; // airtable, postgres, or file
  }
  
  async log(event) {
    const logEntry = {
      timestamp: new Date().toISOString(),
      date: new Date().toISOString().split('T')[0],
      ...event
    };
    
    await this.storage.insert('content_logs', logEntry);
  }
  
  async logGeneration(workflowId, executionId, canonicalId, contentType, aiResponse) {
    await this.log({
      workflow_id: workflowId,
      execution_id: executionId,
      canonical_id: canonicalId,
      content_type: contentType,
      action: "ai_generation",
      result: "success",
      duration_ms: aiResponse.duration,
      metadata: {
        ai: {
          model: aiResponse.model,
          tokens_total: aiResponse.usage.total_tokens,
          cost_usd: aiResponse.cost
        }
      }
    });
  }
  
  async logError(workflowId, executionId, error) {
    await this.log({
      workflow_id: workflowId,
      execution_id: executionId,
      action: "api_error",
      result: "failure",
      metadata: {
        error: {
          type: error.name,
          message: error.message,
          stack: error.stack
        }
      }
    });
  }
}

// Usage
const logger = new ContentLogger(airtableStorage);

await logger.logGeneration(
  "content-generation-blog",
  "exec_abc123",
  "article_009",
  "blog-post",
  aiResponse
);
```

---

## Alerting Rules

Set up alerts based on log data:

```javascript
const ALERT_RULES = {
  // Cost alerts
  high_cost: {
    condition: "cost.total_usd > 0.10",
    action: "slack_alert",
    message: "High cost generation: {canonical_id} cost ${cost.total_usd}"
  },
  
  // Quality alerts
  low_quality: {
    condition: "quality.score < 0.70",
    action: "slack_alert",
    message: "Low quality content: {canonical_id} scored {quality.score}"
  },
  
  // Error rate alerts
  high_error_rate: {
    condition: "error_rate_last_hour > 0.25",
    action: "email_alert",
    message: "Error rate elevated: {error_rate}% in last hour"
  },
  
  // Budget alerts
  budget_exceeded: {
    condition: "daily_spend > daily_budget",
    action: "pause_workflow",
    message: "Daily budget exceeded: ${daily_spend} > ${daily_budget}"
  }
};
```

---

## Checklist for Logging Implementation

☐ Log schema defined for all content types  
☐ Storage system selected (Airtable, Postgres, or file)  
☐ Logging utility created  
☐ All workflows instrumented  
☐ Indexes created for common queries  
☐ Aggregation queries tested  
☐ Alert rules configured  
☐ Dashboard created for monitoring  
☐ Log retention policy defined  

---

_Built by Trending Society · Aligned with Core Rule 8_

