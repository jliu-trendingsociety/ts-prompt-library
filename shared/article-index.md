# Trending Society Article Index

> **Purpose:** Reference for internal linking opportunities  
> **Updated:** December 2025  
> **Rule Alignment:** Core Rule 1 - Immutable IDs

Reference these existing articles when keywords match. Add new articles as published.

**IMPORTANT:** Always use `canonical_id` for tracking and analytics. URLs may change, but canonical IDs are permanent.

| Canonical ID | Keyword/Topic                 | Article Title                                                              | Current URL                                                   | Previous URLs |
| ------------ | ----------------------------- | -------------------------------------------------------------------------- | ------------------------------------------------------------- | ------------- |
| article_001  | AI                            | Gemini tops Google's trending searches in 2025                             | `/blogs/news/gemini-tops-google-trending-searches-2025`       | []            |
| article_002  | deepfake, creator economy     | YouTube's deepfake detection tool and the biometric data tradeoff          | `/blogs/news/youtube-deepfake-detection-tool-biometric-data`  | []            |
| article_003  | AI coding, startups           | Amazon's Kiro AI coding tool: free year for startups explained             | `/blogs/news/amazon-kiro-ai-coding-tool-startups`             | []            |
| article_004  | Sam Altman, OpenAI, AI device | Sam Altman's "Calm AI Device" Vision: Life Beyond the iPhone               | `/blogs/news/sam-altman-calm-ai-device-vision`                | []            |
| article_005  | Warner Music, Suno, AI music  | Warner Music's Landmark AI Deal With Suno, Explained                       | `/blogs/news/warner-music-suno-ai-deal-explained`             | []            |
| article_006  | go-to-market, GTM             | How AI Is Rewriting Go-To-Market: Lessons From OpenAI, Google, And GTMfund | `/blogs/news/ai-rewriting-go-to-market-openai-google-gtmfund` | []            |
| article_007  | construction, AI boom         | How Construction Workers Are Quietly Cashing In on the AI Boom             | `/blogs/news/construction-workers-ai-boom`                    | []            |
| article_008  | AWS, AI agents                | AWS Unveils Powerful New Capabilities for Its AI Agent Builder             | `/blogs/news/aws-ai-agent-builder-capabilities`               | []            |
| service_001  | automation                    | Business Automation Workflows                                              | `/services/business-automation-workflows`                     | []            |
| service_002  | custom GPT, AI agents         | Custom GPT Development                                                     | `/services/custom-gpt-development`                            | []            |
| service_003  | SEO, local SEO                | SEO & GEO Optimization                                                     | `/services/seo-geo-optimization`                              | []            |
| service_004  | Shopify, social commerce      | Social Commerce & Shopify Builds                                           | `/services/social-commerce-shopify-builds`                    | []            |

---

## Usage Instructions

### For Content Generation

When writing, scan for keyword matches and hyperlink them to internal articles/services where contextually appropriate. Aim for 2-5 internal links per article.

**Always use the Current URL for hyperlinks in content.**

### For Analytics & Tracking

Use `canonical_id` for:
- Content performance tracking
- Internal link graph analysis
- A/B test attribution
- Cross-reference with Airtable records
- Historical analysis (even after URL changes)

### When Adding New Articles

```javascript
// 1. Generate new canonical_id
const canonical_id = `article_${String(nextNumber).padStart(3, '0')}`;
// or use Airtable Record ID: `rec_abc123`

// 2. Add to index
{
  canonical_id: "article_009",
  keyword: "new topic",
  title: "Article Title",
  current_url: "/blogs/news/article-slug",
  previous_urls: []
}

// 3. Store in analytics DB
await db.insert("content_index", {
  canonical_id: "article_009",
  created_at: new Date().toISOString(),
  url: "/blogs/news/article-slug"
});
```

### When Changing URLs (Slug Updates)

```javascript
// 1. Move current_url to previous_urls array
previous_urls: ["/blogs/news/old-slug"]

// 2. Update current_url
current_url: "/blogs/news/new-slug"

// 3. Create 301 redirect (Shopify or server-level)
redirects.add({
  from: "/blogs/news/old-slug",
  to: "/blogs/news/new-slug",
  type: 301,
  canonical_id: "article_009"
});

// 4. Analytics continue working (tied to canonical_id, not URL)
```

---

## Redirect Handler (Implementation Guide)

When a URL changes, automatically create redirects:

```javascript
// n8n workflow or Shopify script
async function handleSlugChange(canonical_id, oldUrl, newUrl) {
  // 1. Update article index
  await updateArticleIndex(canonical_id, {
    current_url: newUrl,
    previous_urls: [...article.previous_urls, oldUrl]
  });
  
  // 2. Create 301 redirect (Shopify)
  await shopify.redirects.create({
    path: oldUrl,
    target: newUrl
  });
  
  // 3. Update sitemap
  await regenerateSitemap();
  
  // 4. Log change
  await db.insert("url_changes", {
    canonical_id,
    old_url: oldUrl,
    new_url: newUrl,
    changed_at: new Date().toISOString(),
    redirect_created: true
  });
}
```

---

## Integration with Airtable

If using Airtable for content management, use Airtable Record IDs as canonical_id:

```javascript
// Airtable record
{
  id: "recABC123def456",  // This becomes canonical_id
  fields: {
    title: "Article Title",
    slug: "current-slug",
    slug_history: ["old-slug-1", "old-slug-2"],
    published_at: "2025-12-06",
    keywords: ["AI", "automation"]
  }
}

// Article index entry
{
  canonical_id: "recABC123def456",  // Immutable Airtable ID
  keyword: "AI, automation",
  title: "Article Title",
  current_url: "/blogs/news/current-slug",
  previous_urls: ["old-slug-1", "old-slug-2"]
}
```

---

## Link Audit Tool

Use this to verify all internal links still work:

```javascript
async function auditInternalLinks() {
  const articles = await loadArticleIndex();
  const brokenLinks = [];
  
  for (const article of articles) {
    // Check current URL is live
    const response = await fetch(article.current_url);
    if (response.status !== 200) {
      brokenLinks.push({
        canonical_id: article.canonical_id,
        url: article.current_url,
        status: response.status
      });
    }
    
    // Check redirects exist for previous URLs
    for (const oldUrl of article.previous_urls) {
      const redirect = await shopify.redirects.find(oldUrl);
      if (!redirect) {
        brokenLinks.push({
          canonical_id: article.canonical_id,
          missing_redirect: oldUrl,
          should_point_to: article.current_url
        });
      }
    }
  }
  
  return brokenLinks;
}
```

---

_Built by Trending Society · Aligned with Core Rule 1_

