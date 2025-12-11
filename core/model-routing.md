# AI Model Routing & Cost Control

> **Purpose:** Intelligent model selection and cost optimization for content generation  
> **Version:** 1.0  
> **Updated:** December 2025  
> **Rule Alignment:** Core Rule 9 - AI Cost Control

---

## Philosophy

**Route intelligently. Monitor relentlessly.**

Every content generation task has different complexity requirements. Use the cheapest model that can reliably deliver quality results.

---

## Model Routing Matrix

| Task Type | Model | Avg Tokens | Est Cost | Reasoning |
|-----------|-------|------------|----------|-----------|
| **Social - Single Post** | Gemini Flash 2.0 | 500-800 | $0.0004 | Simple classification, extraction, formatting |
| **Social - Thread (5-8 posts)** | Gemini Pro 1.5 | 1,200-1,800 | $0.002 | Moderate complexity, narrative flow |
| **Email - Single** | Gemini Pro 1.5 | 600-1,000 | $0.001 | Template-based, clear structure |
| **Email - Sequence (5)** | GPT-4o-mini | 2,000-3,000 | $0.003 | Coherent multi-email arc |
| **Ads - Single Platform** | Gemini Flash 2.0 | 400-600 | $0.0003 | Formulaic, template-heavy |
| **Ads - Multi-Platform** | GPT-4o-mini | 1,500-2,200 | $0.002 | Platform adaptation required |
| **Images - Prompt Generation** | Gemini Flash 2.0 | 300-500 | $0.0002 | Structured output, predictable |
| **Editorial - Blog Post** | Claude Sonnet 4 | 3,500-5,000 | $0.015 | Complex reasoning, SEO optimization |
| **Editorial - With Research** | Claude Sonnet 4 | 5,000-7,000 | $0.025 | URL fetching, synthesis, linking |
| **Full Distribution Package** | Claude Sonnet 4 | 7,000-10,000 | $0.035 | Multi-format, high complexity |
| **Critical/Brand-Sensitive** | Claude Opus 3.5 | 4,000-6,000 | $0.075 | Founder voice, strategic content |

---

## Cost-Optimized Prompt Versions

### Editorial Module
- **Premium:** `editorial/v5/master-prompt.md` (800 lines) → Claude Sonnet
- **Standard:** `editorial/v5/master-prompt-lite.md` (300 lines) → GPT-4o-mini
- **Express:** Coming soon (150 lines) → Gemini Pro

### Social Module
- **Standard:** `social/formats/all-platforms.md` (314 lines) → GPT-4o-mini
- **Single:** `social/formats/single-post.md` (100 lines) → Gemini Flash

---

## Model Selection Rules

### 1. Start Cheap, Escalate If Needed

```javascript
async function selectModel(taskType, requirements) {
  // Try cheapest model first
  let model = ROUTING_TABLE[taskType].cheap;
  let result = await generate(model, prompt, requirements);
  
  // Quality gate check
  if (!passesQualityGate(result)) {
    // Escalate to mid-tier
    model = ROUTING_TABLE[taskType].mid;
    result = await generate(model, prompt, requirements);
  }
  
  if (!passesQualityGate(result)) {
    // Escalate to premium
    model = ROUTING_TABLE[taskType].premium;
    result = await generate(model, prompt, requirements);
  }
  
  return { result, model, cost: calculateCost(model, result.tokens) };
}
```

### 2. Complexity Triggers

Automatically escalate to premium models when:
- Source material > 5,000 words
- Requires URL fetching and synthesis
- Multiple competing ideas need balancing
- Brand-critical content (founder blog, press release)
- Technical accuracy is paramount

### 3. Cost Triggers

Set budget caps per content type:
```javascript
const BUDGET_CAPS = {
  "social-single-post": 0.001,      // $0.001 max
  "email-single": 0.002,            // $0.002 max
  "blog-post": 0.020,               // $0.02 max
  "full-distribution": 0.050,       // $0.05 max
};
```

If a generation exceeds budget, log warning and route to cheaper model.

---

## Structured Output Optimization

Use JSON mode to reduce token waste:

**Before (prose):**
```text
Generate a LinkedIn post about AI automation.
Output should include:
- The post copy
- Hashtags
- A visual suggestion
```
Response: ~600 tokens (includes preamble, formatting)

**After (structured):**
```json
{
  "post_copy": "string",
  "hashtags": ["array"],
  "visual_suggestion": "string"
}
```
Response: ~400 tokens (pure data, no formatting waste)

**Savings: 33% token reduction**

---

## Token Budget Enforcement

### Prompt Size Limits

| Module | Max Prompt Length | Max Output Length | Total Budget |
|--------|-------------------|-------------------|--------------|
| Social Single | 1,000 tokens | 500 tokens | 1,500 tokens |
| Email Single | 1,500 tokens | 800 tokens | 2,300 tokens |
| Blog Post | 4,000 tokens | 4,000 tokens | 8,000 tokens |
| Full Distribution | 5,000 tokens | 8,000 tokens | 13,000 tokens |

### Implementation

```javascript
function enforceTokenBudget(promptType, promptText, maxOutputTokens) {
  const promptTokens = countTokens(promptText);
  const budget = TOKEN_BUDGETS[promptType];
  
  if (promptTokens > budget.prompt) {
    throw new Error(`Prompt exceeds budget: ${promptTokens} > ${budget.prompt}`);
  }
  
  return {
    max_tokens: Math.min(maxOutputTokens, budget.output),
    prompt_tokens: promptTokens,
    budget_remaining: budget.total - promptTokens
  };
}
```

---

## Pre-Processing to Reduce Tokens

### 1. HTML to Markdown Conversion

**Before:**
```html
<div class="article">
  <h1>Title</h1>
  <p>Paragraph...</p>
</div>
```
~200 tokens

**After:**
```markdown
# Title

Paragraph...
```
~120 tokens

**Savings: 40% reduction**

### 2. Content Extraction

Only send relevant content to AI:
- Strip navigation, footers, ads
- Extract article body only
- Remove duplicate content
- Compress whitespace

```javascript
function extractRelevantContent(html) {
  // Use readability algorithm
  const article = new Readability(html).parse();
  
  // Convert to markdown
  const markdown = htmlToMarkdown(article.content);
  
  // Compress
  return markdown.replace(/\n{3,}/g, '\n\n').trim();
}
```

---

## Fact Persistence (Reduce Repeated Context)

Don't repeat the same facts in every prompt. Store in database.

**Bad:**
```text
[Every prompt includes:]
Voice: Write as Jeffrey Liu, Founder of Trending Society
Services: Social Commerce, Automation, Custom GPT...
Brand colors: #1a1a2e, #4361ee...
```

**Good:**
```javascript
// Store once in DB
const brandContext = await db.get("brand_context");

// Reference by ID in prompt
const prompt = `
Context ID: brand_ctx_001
Generate LinkedIn post about ${topic}
`;

// AI retrieves context as needed
```

**Savings: 500-1,000 tokens per generation**

---

## Monitoring & Alerting

### Log Every Generation

```javascript
const generationLog = {
  timestamp: new Date().toISOString(),
  content_type: "blog-post",
  prompt_version: "v5",
  model_used: "claude-sonnet-4",
  tokens_prompt: 3842,
  tokens_output: 4123,
  tokens_total: 7965,
  cost: 0.0239,
  quality_score: 0.92,
  duration_ms: 12340,
  canonical_id: "rec_abc123"
};
```

### Cost Alerts

Set up thresholds:
```javascript
const COST_ALERTS = {
  daily: 50.00,      // Alert if daily spend > $50
  weekly: 300.00,    // Alert if weekly spend > $300
  per_item: 0.10,    // Alert if single generation > $0.10
};
```

### Quality vs Cost Tracking

Track which models produce best quality per dollar:
```javascript
const qualityPerDollar = {
  model: "gemini-flash-2.0",
  avg_quality_score: 0.85,
  avg_cost: 0.0004,
  quality_per_dollar: 2125  // 0.85 / 0.0004
};
```

---

## Model-Specific Tips

### Gemini Flash 2.0
- Best for: Classification, extraction, simple formatting
- Weakness: Creative narrative, complex reasoning
- Token limit: 1M context, 8K output
- Cost: $0.075 per 1M input tokens

### Gemini Pro 1.5
- Best for: Summarization, moderate generation
- Weakness: Deep technical accuracy
- Token limit: 2M context, 8K output
- Cost: $1.25 per 1M input tokens

### GPT-4o-mini
- Best for: Balanced quality and cost
- Weakness: Very long context understanding
- Token limit: 128K context, 16K output
- Cost: $0.150 per 1M input tokens

### Claude Sonnet 4
- Best for: Complex reasoning, SEO optimization, code
- Weakness: Cost (10x more than Flash)
- Token limit: 200K context, 8K output
- Cost: $3.00 per 1M input tokens

### Claude Opus 3.5
- Best for: Critical decisions, brand-sensitive content
- Weakness: Cost (50x more than Flash), slower
- Token limit: 200K context, 4K output
- Cost: $15.00 per 1M input tokens

---

## Quick Reference: Model Selection

```
Is it social media (single post)? → Gemini Flash
Is it a simple email? → Gemini Pro
Is it ads or images? → Gemini Flash
Is it a blog post? → Claude Sonnet
Is it full distribution? → Claude Sonnet
Is it founder-voice critical? → Claude Opus
Is it breaking or highly technical? → Claude Opus
```

---

## Cost Benchmarks (Monthly)

Estimated costs for typical Trending Society usage:

| Activity | Volume | Model | Monthly Cost |
|----------|--------|-------|--------------|
| Social posts | 100/month | Gemini Flash | $0.04 |
| Email sequences | 20/month | Gemini Pro | $0.04 |
| Blog posts | 20/month | Claude Sonnet | $6.00 |
| Ads campaigns | 10/month | Gemini Flash | $0.003 |
| Image prompts | 50/month | Gemini Flash | $0.01 |
| **TOTAL** | - | - | **~$6.08/month** |

**With optimization, content generation should cost < $10/month.**

---

## Implementation Checklist

Before deploying a new prompt or workflow:

☐ Model selected based on routing matrix  
☐ Token budget defined and enforced  
☐ Structured output format specified  
☐ HTML→Markdown conversion implemented  
☐ Relevant content extraction active  
☐ Logging enabled with cost tracking  
☐ Quality gates defined  
☐ Fallback model specified  
☐ Budget alert configured  
☐ A/B test plan for model comparison  

---

## Integration with n8n

Example n8n workflow:

```javascript
// 1. Route Request Node
const taskType = $json.content_type;
const model = selectModel(taskType);

// 2. Pre-process Node
const cleanedContent = extractRelevantContent($json.source_html);
const tokenCount = countTokens(cleanedContent);

// 3. AI Generation Node
const result = await ai.generate({
  model: model.name,
  prompt: buildPrompt(taskType, cleanedContent),
  max_tokens: model.max_output_tokens,
  response_format: { type: "json_object" }
});

// 4. Log Node
await db.insert("generation_logs", {
  ...generationLog,
  model: model.name,
  cost: calculateCost(model, result.usage)
});

// 5. Quality Gate Node
if (!passesQualityGate(result)) {
  // Retry with premium model
  return escalateToPremium();
}

return result;
```

---

_Built by Trending Society · Aligned with Core Rule 9_

