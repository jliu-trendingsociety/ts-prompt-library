# Circuit Breakers for External Resources

> **Purpose:** Graceful degradation when external dependencies fail  
> **Rule Alignment:** Core Rule 7 - Circuit Breaker  
> **Updated:** December 2025

---

## Philosophy

**Graceful degradation, never full crash.**

External resources fail. Logo URLs break. APIs go down. News sites block scrapers.

One bad external dependency should never kill your entire content generation system. Circuit breakers prevent cascading failures.

---

## The Problem

Current prompts reference external resources that can fail:

### 1. Logo URLs in Source Registry

```markdown
50:67:prompts/shared/source-registry.md
| TechCrunch | techcrunch.com | `https://techcrunch.com/.../favicon.png` |
```

**Risk:** If TechCrunch changes their favicon URL:

- Editorial content generation fails (missing badge logo)
- 500 articles with broken images
- Manual fix required for each article

### 2. External Link References

```html
<a href="https://openai.com" target="_blank">OpenAI</a>
```

**Risk:** If site is down or blocks requests:

- Link validation fails
- Content generation pauses
- Manual intervention needed

### 3. Source Content Fetching

```javascript
const article = await fetch(sourceUrl);
```

**Risk:** Timeouts, 404s, paywalls, rate limits

---

## Solution Architecture

### Pattern 1: Fallback Chain

Try primary → fallback 1 → fallback 2 → graceful failure

```javascript
async function getResourceWithFallback(resource) {
  const attempts = [
    () => getPrimary(resource),
    () => getFallback1(resource),
    () => getFallback2(resource),
    () => getDefaultFallback(resource),
  ];

  for (const attempt of attempts) {
    try {
      return await attempt();
    } catch (error) {
      console.log(`Attempt failed: ${error.message}`);
      // Continue to next fallback
    }
  }

  // All fallbacks exhausted
  throw new Error(`All fallbacks failed for: ${resource}`);
}
```

### Pattern 2: Circuit Breaker State Machine

Track failures and pause after threshold.

```javascript
class CircuitBreaker {
  constructor(name, options = {}) {
    this.name = name;
    this.failures = 0;
    this.successes = 0;
    this.lastFailure = null;
    this.state = 'CLOSED'; // CLOSED, OPEN, HALF_OPEN

    this.threshold = options.threshold || 3;
    this.timeout = options.timeout || 60000; // 1 minute
    this.halfOpenAttempts = options.halfOpenAttempts || 1;
  }

  async execute(fn) {
    if (this.state === 'OPEN') {
      if (Date.now() - this.lastFailure > this.timeout) {
        console.log(`[${this.name}] Attempting to recover (HALF_OPEN)`);
        this.state = 'HALF_OPEN';
      } else {
        throw new Error(`Circuit breaker OPEN for: ${this.name}`);
      }
    }

    try {
      const result = await fn();
      this.onSuccess();
      return result;
    } catch (error) {
      this.onFailure();
      throw error;
    }
  }

  onSuccess() {
    this.failures = 0;
    this.successes++;

    if (this.state === 'HALF_OPEN') {
      console.log(`[${this.name}] Recovery successful (CLOSED)`);
      this.state = 'CLOSED';
    }
  }

  onFailure() {
    this.failures++;
    this.lastFailure = Date.now();

    if (this.failures >= this.threshold) {
      console.log(
        `[${this.name}] Circuit breaker OPEN after ${this.failures} failures`
      );
      this.state = 'OPEN';
    }
  }

  reset() {
    this.failures = 0;
    this.successes = 0;
    this.lastFailure = null;
    this.state = 'CLOSED';
  }
}

// Usage
const techCrunchBreaker = new CircuitBreaker('techcrunch-logo', {
  threshold: 3,
  timeout: 300000, // 5 minutes
});

async function getTechCrunchLogo() {
  return await techCrunchBreaker.execute(async () => {
    const response = await fetch('https://techcrunch.com/favicon.png');
    if (!response.ok) throw new Error(`HTTP ${response.status}`);
    return response.url;
  });
}
```

---

## Fix #1: Logo URL Circuit Breaker

### Step 1: Host Logos on CDN (Cloudinary)

Upload all source logos to Cloudinary for reliability:

```javascript
// One-time migration script (run as standalone Node.js script, NOT in n8n Function node)
// Can use require() because this runs outside n8n
const SOURCES = require('./source-registry.json');

async function migrateLogosToCloudinary() {
  for (const source of SOURCES) {
    try {
      // Download original logo
      const response = await fetch(source.logo_url);
      const buffer = await response.buffer();

      // Upload to Cloudinary
      const result = await cloudinary.uploader.upload(buffer, {
        folder: 'trending-society/source-logos',
        public_id: source.name.toLowerCase().replace(/\s+/g, '-'),
        resource_type: 'image',
        format: 'png',
      });

      console.log(`Uploaded: ${source.name} → ${result.secure_url}`);

      // Update source registry
      source.logo_url_cdn = result.secure_url;
      source.logo_url_original = source.logo_url;
    } catch (error) {
      console.error(`Failed: ${source.name}`, error.message);
    }
  }

  // Save updated registry
  await fs.writeFile(
    './source-registry-updated.json',
    JSON.stringify(SOURCES, null, 2)
  );
}
```

**⚠️ Note:** This is a standalone Node.js script (run with `node migrate-logos.js`), **not an n8n Function node**. For n8n implementation, use HTTP Request nodes to upload to Cloudinary and Airtable nodes to store results.

### Step 2: Update Source Registry with Fallbacks

```markdown
| Source Name | Domain         | Primary Logo (CDN)                                                                        | Fallback Logo                                                    | Original URL                             | Last Verified |
| ----------- | -------------- | ----------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ---------------------------------------- | ------------- |
| TechCrunch  | techcrunch.com | `https://res.cloudinary.com/trending-society/image/upload/v1/source-logos/techcrunch.png` | `https://www.google.com/s2/favicons?domain=techcrunch.com&sz=64` | `https://techcrunch.com/.../favicon.png` | Dec 2025      |
```

### Step 3: Implement Fallback Logic

```javascript
async function getSourceLogo(sourceName) {
  const source = SOURCES.find((s) => s.name === sourceName);

  if (!source) {
    return DEFAULT_LOGO;
  }

  // Try CDN first (reliable)
  try {
    const response = await fetch(source.logo_url_cdn, { method: 'HEAD' });
    if (response.ok) return source.logo_url_cdn;
  } catch (error) {
    console.log(`CDN failed for ${sourceName}`);
  }

  // Try Google favicon service (reliable fallback)
  try {
    const googleFavicon = `https://www.google.com/s2/favicons?domain=${source.domain}&sz=64`;
    const response = await fetch(googleFavicon, { method: 'HEAD' });
    if (response.ok) return googleFavicon;
  } catch (error) {
    console.log(`Google favicon failed for ${sourceName}`);
  }

  // Try original URL (may be broken)
  try {
    const response = await fetch(source.logo_url_original, { method: 'HEAD' });
    if (response.ok) return source.logo_url_original;
  } catch (error) {
    console.log(`Original URL failed for ${sourceName}`);
  }

  // Return default logo
  console.log(`All sources failed for ${sourceName}, using default`);
  return DEFAULT_LOGO;
}
```

---

## Fix #2: External Link Validation Circuit Breaker

Don't fail content generation if external links are temporarily down.

### Pattern: Validate with Timeout, Don't Block

```javascript
async function validateLinks(html, options = {}) {
  const timeout = options.timeout || 5000;
  const links = extractLinks(html);
  const results = [];

  for (const link of links) {
    try {
      const controller = new AbortController();
      const timeoutId = setTimeout(() => controller.abort(), timeout);

      const response = await fetch(link.url, {
        method: 'HEAD',
        signal: controller.signal,
      });

      clearTimeout(timeoutId);

      results.push({
        url: link.url,
        status: 'valid',
        http_code: response.status,
      });
    } catch (error) {
      // DON'T fail generation, just log
      results.push({
        url: link.url,
        status: 'unvalidated',
        reason: error.message,
      });
    }
  }

  return results;
}

// In content generation workflow
const linkValidation = await validateLinks(generatedContent, { timeout: 3000 });

// Log but don't fail
await logger.log({
  action: 'link_validation',
  result: 'success', // Even if some links failed
  metadata: {
    links_checked: linkValidation.length,
    links_valid: linkValidation.filter((l) => l.status === 'valid').length,
    links_unvalidated: linkValidation.filter((l) => l.status === 'unvalidated')
      .length,
  },
});

// Continue with publishing
```

---

## Fix #3: Source Content Fetch Circuit Breaker

### Pattern: Timeout + Retry + Fallback

```javascript
class ContentFetcher {
  constructor() {
    this.breakers = new Map();
  }

  getBreaker(domain) {
    if (!this.breakers.has(domain)) {
      this.breakers.set(
        domain,
        new CircuitBreaker(domain, {
          threshold: 3,
          timeout: 300000, // 5 minutes
        })
      );
    }
    return this.breakers.get(domain);
  }

  async fetch(url, options = {}) {
    const domain = new URL(url).hostname;
    const breaker = this.getBreaker(domain);
    const timeout = options.timeout || 10000;

    try {
      return await breaker.execute(async () => {
        const controller = new AbortController();
        const timeoutId = setTimeout(() => controller.abort(), timeout);

        const response = await fetch(url, {
          signal: controller.signal,
          headers: {
            'User-Agent': 'TrendingSocietyBot/1.0',
            ...options.headers,
          },
        });

        clearTimeout(timeoutId);

        if (!response.ok) {
          throw new Error(`HTTP ${response.status}`);
        }

        return await response.text();
      });
    } catch (error) {
      // Circuit is open, try cached version
      if (error.message.includes('Circuit breaker OPEN')) {
        console.log(`Circuit open for ${domain}, trying cache`);
        return await this.getCachedContent(url);
      }

      throw error;
    }
  }

  async getCachedContent(url) {
    // Try Airtable cache or similar
    const cached = await db.get('content_cache', { url });
    if (cached && Date.now() - cached.fetched_at < 86400000) {
      // 24 hours
      console.log(`Using cached content for ${url}`);
      return cached.content;
    }

    throw new Error(`No cached content available for ${url}`);
  }
}

// Usage
const fetcher = new ContentFetcher();

try {
  const content = await fetcher.fetch(sourceUrl, { timeout: 10000 });
} catch (error) {
  // Graceful fallback: use URL only, no content extraction
  console.log(`Content fetch failed: ${error.message}`);
  console.log(`Proceeding with URL-only mode`);

  const content = {
    url: sourceUrl,
    title: 'Source Article', // From metadata or user input
    excerpt: '',
    mode: 'url-only',
  };
}
```

---

## Implementation in Prompts

### Update Source Registry

<function_calls>
<invoke name="search_replace">
<parameter name="file_path">/Users/jeff/Documents/GitHub/trending-society-cursor-rules/prompts/shared/source-registry.md
