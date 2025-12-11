# Idempotency Guide for Content Generation

> **Purpose:** Safely retryable content operations  
> **Rule Alignment:** Core Rule 5 - Idempotency by Default  
> **Updated:** December 2025

---

## Philosophy

**Every operation must be safely retryable.**

Content generation workflows fail. APIs timeout. Networks drop. LLMs hallucinate. Retries happen.

Idempotency ensures that running the same operation twice produces the same result and doesn't create duplicates.

---

## The Problem

### Without Idempotency

```javascript
// BAD: Creates duplicate on retry
async function generateBlogPost(sourceUrl) {
  const content = await ai.generate(prompt, sourceUrl);
  const article = await shopify.articles.create(content);
  return article;
}

// First run: Creates article_001
// Retry (after timeout): Creates article_002 (DUPLICATE!)
```

### With Idempotency

```javascript
// GOOD: Safe to retry
async function generateBlogPost(sourceUrl, idempotencyKey) {
  // Check if already processed
  const existing = await db.get('content_index', { 
    source_url: sourceUrl,
    idempotency_key: idempotencyKey 
  });
  
  if (existing) {
    console.log('Already processed, returning existing result');
    return existing;
  }
  
  // Generate new content
  const content = await ai.generate(prompt, sourceUrl);
  
  // Upsert (create or update)
  const article = await shopify.articles.upsert(content, {
    unique_key: idempotencyKey
  });
  
  // Store record
  await db.upsert('content_index', {
    source_url: sourceUrl,
    idempotency_key: idempotencyKey,
    article_id: article.id,
    canonical_id: article.canonical_id
  });
  
  return article;
}

// First run: Creates article_001
// Retry: Returns article_001 (NO DUPLICATE!)
```

---

## Idempotency Strategies

### 1. Deduplication Keys

Generate a unique key per operation, store before processing.

```javascript
function generateIdempotencyKey(input) {
  // Hash the inputs to create deterministic key
  const hash = crypto.createHash('sha256');
  hash.update(JSON.stringify({
    source_url: input.sourceUrl,
    content_type: input.contentType,
    prompt_version: input.promptVersion,
    date: new Date().toISOString().split('T')[0]  // Same day = same key
  }));
  return hash.digest('hex');
}

// Usage
const idempotencyKey = generateIdempotencyKey({
  sourceUrl: 'https://example.com/article',
  contentType: 'blog-post',
  promptVersion: 'v5'
});

// Store key BEFORE processing
await db.insert('processing_queue', {
  idempotency_key: idempotencyKey,
  status: 'pending',
  created_at: new Date().toISOString()
});

// Process
const result = await generateContent(input, idempotencyKey);

// Update status
await db.update('processing_queue', { idempotency_key: idempotencyKey }, {
  status: 'completed',
  result_id: result.id
});
```

### 2. Check-Before-Write Pattern

Always check if content exists before creating.

```javascript
async function publishArticle(content) {
  // Generate canonical_id from content
  const canonical_id = `article_${slugify(content.title)}`;
  
  // Check if already exists
  const existing = await shopify.articles.findBy({ canonical_id });
  
  if (existing) {
    console.log(`Article ${canonical_id} already exists`);
    
    // Decide: update or skip
    if (shouldUpdate(existing, content)) {
      return await shopify.articles.update(existing.id, content);
    } else {
      return existing;  // Skip, return existing
    }
  }
  
  // Doesn't exist, create new
  return await shopify.articles.create({
    ...content,
    canonical_id
  });
}
```

### 3. Upsert (Update or Insert)

Use database upsert operations when supported.

```javascript
// Airtable example
async function storeArticleMetadata(article) {
  await airtable.upsert({
    table: 'Articles',
    fields: {
      canonical_id: article.canonical_id,
      title: article.title,
      url: article.url,
      published_at: article.published_at,
      word_count: article.word_count
    },
    mergeOn: ['canonical_id']  // Unique constraint
  });
}

// PostgreSQL example
await db.query(`
  INSERT INTO articles (canonical_id, title, url, published_at)
  VALUES ($1, $2, $3, $4)
  ON CONFLICT (canonical_id) 
  DO UPDATE SET
    title = EXCLUDED.title,
    url = EXCLUDED.url,
    published_at = EXCLUDED.published_at,
    updated_at = NOW()
`, [canonical_id, title, url, published_at]);
```

### 4. State Machine with External Storage

Store processing state externally, resume from where you left off.

```javascript
async function processContentWithState(sourceUrl) {
  // Get or create state record
  let state = await db.get('content_state', { source_url: sourceUrl });
  
  if (!state) {
    state = {
      source_url: sourceUrl,
      stage: 'initialized',
      data: {}
    };
    await db.insert('content_state', state);
  }
  
  // Stage 1: Fetch content
  if (state.stage === 'initialized') {
    state.data.raw_content = await fetchContent(sourceUrl);
    state.stage = 'content_fetched';
    await db.update('content_state', { source_url: sourceUrl }, state);
  }
  
  // Stage 2: Generate with AI
  if (state.stage === 'content_fetched') {
    state.data.generated_content = await ai.generate(
      prompt, 
      state.data.raw_content
    );
    state.stage = 'content_generated';
    await db.update('content_state', { source_url: sourceUrl }, state);
  }
  
  // Stage 3: Publish
  if (state.stage === 'content_generated') {
    state.data.article_id = await publishArticle(state.data.generated_content);
    state.stage = 'published';
    await db.update('content_state', { source_url: sourceUrl }, state);
  }
  
  return state.data;
}

// Retry-safe: If it fails at any stage, next run resumes from that stage
```

---

## Content Generation Idempotency Patterns

### Pattern 1: Source URL as Dedup Key

For news article ingestion:

```javascript
async function ingestArticle(sourceUrl) {
  // Source URL is natural dedup key
  const existing = await db.get('articles', { source_url: sourceUrl });
  
  if (existing) {
    const age = Date.now() - new Date(existing.processed_at).getTime();
    
    // Don't re-process if < 24 hours old
    if (age < 86400000) {
      console.log('Article already processed recently');
      return existing;
    }
    
    // Update stale content
    console.log('Updating stale article');
    return await updateArticle(existing.canonical_id, sourceUrl);
  }
  
  // New article, process
  return await processNewArticle(sourceUrl);
}
```

### Pattern 2: Content Hash for Duplicate Detection

Detect if generated content is duplicate even with different input:

```javascript
function hashContent(content) {
  // Normalize and hash
  const normalized = content
    .toLowerCase()
    .replace(/\s+/g, ' ')
    .replace(/[^\w\s]/g, '')
    .trim();
  
  return crypto.createHash('sha256').update(normalized).digest('hex');
}

async function publishWithDuplicateCheck(content) {
  const contentHash = hashContent(content.body);
  
  // Check if similar content exists
  const duplicate = await db.get('articles', { content_hash: contentHash });
  
  if (duplicate) {
    console.log('Duplicate content detected');
    throw new Error(`Content too similar to existing article: ${duplicate.canonical_id}`);
  }
  
  // Store with hash
  return await db.insert('articles', {
    ...content,
    content_hash: contentHash
  });
}
```

### Pattern 3: Date-Based Idempotency for Scheduled Content

For daily/weekly automated content:

```javascript
async function generateDailyNewsletter(date) {
  const idempotencyKey = `newsletter-${date}`;  // e.g., "newsletter-2025-12-06"
  
  // Check if already generated
  const existing = await db.get('newsletters', { idempotency_key: idempotencyKey });
  
  if (existing) {
    console.log(`Newsletter for ${date} already generated`);
    return existing;
  }
  
  // Generate newsletter
  const content = await generateContent(date);
  
  // Store with idempotency key
  return await db.insert('newsletters', {
    idempotency_key: idempotencyKey,
    date,
    content,
    generated_at: new Date().toISOString()
  });
}
```

### Pattern 4: Execution ID for Workflow Runs

For n8n workflows:

```javascript
// In n8n Function node
const executionId = $execution.id;

// Check if this execution already completed
const existing = await db.get('workflow_executions', { 
  execution_id: executionId,
  node: $node.name
});

if (existing && existing.status === 'completed') {
  console.log('Node already executed in this run');
  return existing.result;
}

// Process
const result = await processContent($json);

// Store result
await db.upsert('workflow_executions', {
  execution_id: executionId,
  node: $node.name,
  status: 'completed',
  result: result,
  completed_at: new Date().toISOString()
}, { mergeOn: ['execution_id', 'node'] });

return result;
```

---

## Shopify-Specific Idempotency

### Problem: Shopify Doesn't Support Upsert on Articles

You must implement your own deduplication:

```javascript
async function publishToShopify(content, canonicalId) {
  // 1. Check Airtable for existing Shopify article ID
  const record = await airtable.findBy('articles', { 
    canonical_id: canonicalId 
  });
  
  if (record && record.shopify_article_id) {
    // Update existing
    console.log(`Updating existing article: ${record.shopify_article_id}`);
    
    await shopify.articles.update(record.shopify_article_id, {
      title: content.title,
      body_html: content.body_html,
      tags: content.tags.join(', ')
    });
    
    return record;
  }
  
  // 2. Double-check Shopify (in case Airtable is out of sync)
  const existingInShopify = await shopify.articles.list({
    title: content.title,
    limit: 1
  });
  
  if (existingInShopify.length > 0) {
    console.log('Found article in Shopify, syncing to Airtable');
    
    await airtable.upsert('articles', {
      canonical_id: canonicalId,
      shopify_article_id: existingInShopify[0].id
    }, { mergeOn: ['canonical_id'] });
    
    return existingInShopify[0];
  }
  
  // 3. Create new
  console.log('Creating new article in Shopify');
  
  const article = await shopify.articles.create({
    blog_id: BLOG_ID,
    title: content.title,
    body_html: content.body_html,
    tags: content.tags.join(', ')
  });
  
  // 4. Store mapping
  await airtable.upsert('articles', {
    canonical_id: canonicalId,
    shopify_article_id: article.id,
    title: content.title,
    url: `/blogs/news/${article.handle}`,
    created_at: new Date().toISOString()
  }, { mergeOn: ['canonical_id'] });
  
  return article;
}
```

---

## Retry Logic with Idempotency

```javascript
async function withRetry(fn, options = {}) {
  const maxRetries = options.maxRetries || 3;
  const baseDelay = options.baseDelay || 1000;
  const idempotencyKey = options.idempotencyKey;
  
  for (let attempt = 0; attempt < maxRetries; attempt++) {
    try {
      // Always pass idempotency key to function
      return await fn(idempotencyKey);
      
    } catch (error) {
      // Don't retry client errors (400-level)
      if (error.status >= 400 && error.status < 500 && error.status !== 429) {
        throw error;
      }
      
      // Last attempt, give up
      if (attempt === maxRetries - 1) {
        throw error;
      }
      
      // Wait and retry
      const delay = baseDelay * Math.pow(2, attempt);
      console.log(`Attempt ${attempt + 1} failed, retrying in ${delay}ms`);
      await sleep(delay);
    }
  }
}

// Usage
await withRetry(
  async (key) => await generateContent(sourceUrl, key),
  { 
    maxRetries: 3,
    idempotencyKey: generateIdempotencyKey({ sourceUrl })
  }
);
```

---

## Testing Idempotency

### Test: Run Twice, Get Same Result

```javascript
describe('Content Generation Idempotency', () => {
  it('should not create duplicates on retry', async () => {
    const sourceUrl = 'https://example.com/test-article';
    const idempotencyKey = generateIdempotencyKey({ sourceUrl });
    
    // First run
    const result1 = await generateBlogPost(sourceUrl, idempotencyKey);
    
    // Second run (simulating retry)
    const result2 = await generateBlogPost(sourceUrl, idempotencyKey);
    
    // Should return same article
    expect(result1.id).toBe(result2.id);
    expect(result1.canonical_id).toBe(result2.canonical_id);
    
    // Should only have one record in DB
    const articles = await db.query('articles', { source_url: sourceUrl });
    expect(articles.length).toBe(1);
  });
  
  it('should handle concurrent requests', async () => {
    const sourceUrl = 'https://example.com/concurrent-test';
    const idempotencyKey = generateIdempotencyKey({ sourceUrl });
    
    // Simulate two simultaneous requests
    const [result1, result2] = await Promise.all([
      generateBlogPost(sourceUrl, idempotencyKey),
      generateBlogPost(sourceUrl, idempotencyKey)
    ]);
    
    // Both should get same result
    expect(result1.id).toBe(result2.id);
  });
});
```

---

## Checklist for Idempotent Operations

Before deploying a content generation workflow:

☐ Idempotency key generated from inputs  
☐ Check-before-write pattern implemented  
☐ Upsert operations used where possible  
☐ Unique constraints on canonical_id  
☐ State stored externally (not in workflow memory)  
☐ Retry logic respects idempotency keys  
☐ Duplicate detection in place  
☐ Tests verify no duplicates on retry  
☐ Concurrent request handling tested  
☐ Logging includes idempotency keys  

---

_Built by Trending Society · Aligned with Core Rule 5_

