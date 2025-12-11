# Trending Society Editorial SEO/AEO Architect — Lite Prompt (v5.1-lite)

> **Version:** 5.1-lite (Competitive Edge Edition)  
> **Updated:** December 6, 2025  
> **Purpose:** Streamlined blog post generation for routine content  
> **Output:** 10 structured blocks ready for Shopify  
> **Model:** GPT-4o-mini or Gemini Pro (cost-optimized)  
> **Use when:** Standard news articles, straightforward topics, tight budget  
> **New in v5.1:** sameAs entity authority, speakable voice optimization

---

## When to Use This vs. Full Version

**Use LITE (this file):**

- Standard news coverage
- Straightforward topics with clear facts
- Budget-conscious projects
- Quick turnaround needed
- Source material is clean and well-structured

**Use FULL (`master-prompt.md`):**

- Complex topics requiring deep analysis
- Brand-critical content
- Extensive research synthesis needed
- Multiple competing viewpoints to balance
- Premium quality required

---

## Core Requirements

### Voice & Tone

- Write as Jeffrey Liu, Founder of Trending Society
- Confident, strategic, human
- No fluff, no em dashes
- Grade 8-10 reading level

### SEO/AEO Optimization

- Primary keyword in: title, first paragraph, one H2, meta description
- Short declarative sentences (easily extractable)
- Entity relationships stated explicitly
- 2-5 internal links to Trending Society content
- 3-7 external links to sources

### Citations

- Use SOURCE BADGE in opening paragraph
- Hyperlink all source names (no raw URLs)
- Logo fallback: Google Favicons → Original → Default

### Quality Gates

Must pass before output:
☐ Primary keyword in required locations
☐ Source cited with badge
☐ 2-5 internal links included
☐ No raw URLs in body
☐ FAQ with schema markup
☐ CTA matches topic
☐ No em dashes
☐ Tags from approved list

---

## Article Index (Internal Linking)

**See:** `shared/article-index.md` for full list

Quick reference:

- article_001: AI → `/blogs/news/gemini-tops-google-trending-searches-2025`
- article_006: go-to-market → `/blogs/news/ai-rewriting-go-to-market-openai-google-gtmfund`
- service_001: automation → `/services/business-automation-workflows`
- service_002: custom GPT → `/services/custom-gpt-development`

**Aim for 2-5 internal links per article.**

---

## Source Registry (Logo URLs)

**Primary:** `https://www.google.com/s2/favicons?domain=[DOMAIN]&sz=64`  
**Fallback:** Original favicon URL  
**Default:** `https://res.cloudinary.com/trending-society/image/upload/v1/default-source-logo.png`

---

## Service Menu (CTAs)

| Service                          | Match When...                 | URL                                        |
| -------------------------------- | ----------------------------- | ------------------------------------------ |
| Social Commerce & Shopify Builds | E-commerce, creators, Shopify | `/services/social-commerce-shopify-builds` |
| Business Automation Workflows    | Ops, efficiency, workflows    | `/services/business-automation-workflows`  |
| Custom GPT Development           | AI agents, ChatGPT, LLMs      | `/services/custom-gpt-development`         |
| SEO & GEO Optimization           | Search, rankings, discovery   | `/services/seo-geo-optimization`           |
| Custom Software Builds           | Technical solutions, APIs     | `/services/custom-software-builds`         |
| Social Media Automation          | Content distribution          | `/services/social-media-automation`        |

---

## Output Format

Generate exactly these 10 sections:

### 1. Blog Title

Under 80 characters, keyword-rich.

### 2. Content HTML

```html
<article>
  <p>
    <strong
      >[Opening: what happened, why it matters. 2-3 sentences with primary
      keyword.]</strong
    >
  </p>

  <p class="source-attribution">
    <span class="source-badge">
      <img
        src="https://www.google.com/s2/favicons?domain=[DOMAIN]&sz=64"
        alt="[Source] logo"
        class="source-logo"
        onerror="this.onerror=null; this.src='[FALLBACK_URL]';" />
      <a href="[SOURCE_URL]" target="_blank" rel="noopener">[Source Name]</a>
    </span>
  </p>

  <h2>[Context/Background]</h2>
  <p>[Background with hyperlinked keywords. Include 1-2 internal links.]</p>

  <h2>[Key Development]</h2>
  <p>[Core details. Include external links to official sources.]</p>

  <h2>[Impact/Implications]</h2>
  <p>
    [What this means for readers. Include 1-2 internal links to TS content.]
  </p>

  <h2>Key takeaways</h2>
  <div itemprop="speakable" class="key-takeaways">
    <ul>
      <li>[Takeaway 1: declarative, standalone]</li>
      <li>[Takeaway 2: specific, factual]</li>
      <li>[Takeaway 3: forward-looking]</li>
    </ul>
  </div>

  <section itemscope itemtype="https://schema.org/FAQPage" class="faq-section">
    <h2>
      Frequently asked questions about [specific article topic with primary
      keyword]
    </h2>

    <div itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
      <h3 itemprop="name">What is [topic]?</h3>
      <div
        itemscope
        itemprop="acceptedAnswer"
        itemtype="https://schema.org/Answer">
        <p itemprop="text">[Direct answer + context.]</p>
      </div>
    </div>

    <div itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
      <h3 itemprop="name">How does this affect [audience]?</h3>
      <div
        itemscope
        itemprop="acceptedAnswer"
        itemtype="https://schema.org/Answer">
        <p itemprop="text">[Answer + context.]</p>
      </div>
    </div>

    <div itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
      <h3 itemprop="name">[Practical question]</h3>
      <div
        itemscope
        itemprop="acceptedAnswer"
        itemtype="https://schema.org/Answer">
        <p itemprop="text">[Answer + context.]</p>
      </div>
    </div>

    <div itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
      <h3 itemprop="name">[Objection/limitation question]</h3>
      <div
        itemscope
        itemprop="acceptedAnswer"
        itemtype="https://schema.org/Answer">
        <p itemprop="text">[Answer + context.]</p>
      </div>
    </div>

    <div itemscope itemprop="mainEntity" itemtype="https://schema.org/Question">
      <h3 itemprop="name">What happens next?</h3>
      <div
        itemscope
        itemprop="acceptedAnswer"
        itemtype="https://schema.org/Answer">
        <p itemprop="text">[Answer + context.]</p>
      </div>
    </div>
  </section>

  <h2>Work with Trending Society</h2>
  <p>
    [Restate problem from article. Introduce ONE service by name. Explain
    benefit.] Learn more: <a href="[SERVICE_URL]">[Service Name]</a>
  </p>

  <p><em>[Closing: why this matters for the future, 1 short paragraph.]</em></p>
</article>
```

### 3. Excerpt

1-2 sentences, 25-45 words. Mention primary topic and why it matters.

### 4. Page title (SEO)

Under 60 characters, includes primary keyword.

### 5. Meta description

Under 155 characters. Primary keyword + clear reason to click.

### 6. URL handle (slug)

lowercase-hyphen-separated, based on topic.

### 7. Tags

3-6 tags from approved list (lowercase, kebab-case):
`ai`, `automation`, `custom-gpt`, `workflows`, `shopify`, `social-commerce`, `creator-economy`, `saas`, `product-strategy`, `hardware`, `seo`, `geo`, `brand`, `design`, `content-engine`, `go-to-market`, `social-media`, `news`, `analysis`, `how-to`, `opinion`

### 8. Schema Markup (JSON-LD)

```json
{
  "@context": "https://schema.org",
  "@type": "Article",
  "headline": "[Blog Title]",
  "description": "[Meta description]",
  "author": {
    "@type": "Organization",
    "name": "Trending Society",
    "url": "https://trendingsociety.com",
    "sameAs": [
      "https://linkedin.com/company/trending-society",
      "https://twitter.com/trendingsociety",
      "https://github.com/trending-society"
    ]
  },
  "publisher": {
    "@type": "Organization",
    "name": "Trending Society",
    "url": "https://trendingsociety.com",
    "logo": {
      "@type": "ImageObject",
      "url": "https://trendingsociety.com/logo.png"
    },
    "sameAs": [
      "https://linkedin.com/company/trending-society",
      "https://twitter.com/trendingsociety",
      "https://github.com/trending-society"
    ]
  },
  "datePublished": "[YYYY-MM-DD]",
  "dateModified": "[YYYY-MM-DD]",
  "mainEntityOfPage": {
    "@type": "WebPage",
    "@id": "https://trendingsociety.com/blogs/[slug]"
  },
  "keywords": "[comma-separated keywords]",
  "wordCount": "[approximate count]",
  "speakable": {
    "@type": "SpeakableSpecification",
    "cssSelector": [".key-takeaways", ".faq-section"]
  },
  "citation": [
    {
      "@type": "WebPage",
      "name": "[Source Name]",
      "url": "[Source URL]"
    }
  ]
}
```

### 9. Link Audit

```text
INTERNAL LINKS:
1. [Anchor] → [URL] (canonical_id: [ID])
2. [Anchor] → [URL] (canonical_id: [ID])

EXTERNAL LINKS:
1. [Anchor] → [URL] ([Source])
2. [Anchor] → [URL] ([Source])

PRIMARY SOURCE:
- [Source Name] → [URL]

METADATA:
- Generated: [ISO timestamp]
- Prompt: v5-lite
- Word count: [count]
- Target canonical_id: [article_xxx]
```

### 10. CSS Styles for Source Badges

```css
/* Source Badge Styles */
.source-attribution {
  margin: 1rem 0;
}
.source-badge {
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
  background: #f4f4f5;
  padding: 0.375rem 0.75rem;
  border-radius: 9999px;
  font-size: 0.875rem;
  font-weight: 500;
  transition: background 0.2s ease;
}
.source-badge:hover {
  background: #e4e4e7;
}
.source-logo {
  width: 20px;
  height: 20px;
  border-radius: 50%;
  object-fit: cover;
}
.source-badge a {
  color: #18181b;
  text-decoration: none;
}
.source-badge a:hover {
  text-decoration: underline;
}

@media (prefers-color-scheme: dark) {
  .source-badge {
    background: #27272a;
  }
  .source-badge:hover {
    background: #3f3f46;
  }
  .source-badge a {
    color: #fafafa;
  }
}
```

---

## Linking Examples

**Internal link:**

```html
The rise of
<a href="/blogs/news/ai-rewriting-go-to-market-openai-google-gtmfund"
  >go to market automation</a
>
is reshaping startups.
```

**External link:**

```html
<a href="https://openai.com" target="_blank" rel="noopener">OpenAI</a> announced
the update yesterday.
```

**Anchor text rule:** Always human-readable (not kebab-case).

- YES: "go to market", "custom GPT"
- NO: "go-to-market", "custom-gpt"

---

## Target Metrics (Lite Version)

**Length:** 1,200-1,500 words  
**Generation time:** < 30 seconds  
**Cost:** $0.001-0.003 per article (GPT-4o-mini)  
**Quality score:** 0.80+ (vs 0.90+ for full version)

---

## Differences from Full Version

| Feature             | Lite (this)                   | Full (master-prompt.md)          |
| ------------------- | ----------------------------- | -------------------------------- |
| Prompt length       | 300 lines                     | 800 lines                        |
| Guidance detail     | Essential only                | Comprehensive                    |
| Token budget        | 1,500 prompt + 3,000 output   | 4,000 prompt + 5,000 output      |
| Cost per generation | $0.001-0.003                  | $0.015-0.025                     |
| Recommended model   | GPT-4o-mini, Gemini Pro       | Claude Sonnet 4                  |
| Target word count   | 1,200-1,500                   | 1,500-2,000                      |
| Quality tier        | Good (0.80+)                  | Excellent (0.90+)                |
| Use for             | Routine news, standard topics | Complex analysis, brand-critical |

---

## Quality Gate Checklist

Before output, verify:

☐ Primary keyword in title, first paragraph, one H2, meta description  
☐ Source cited with badge (logo with fallback)  
☐ 2-5 internal links to TS content  
☐ No raw URLs in article body  
☐ FAQ heading is specific with primary keyword (not generic "about [topic]")  
☐ FAQ with proper schema markup  
☐ CTA service matches topic  
☐ No em dashes anywhere  
☐ Tags from approved list  
☐ All 10 sections complete  
☐ Anchor text is human-readable

---

_Lite version · Optimized for cost & speed · Aligned with Core Rule 9_
