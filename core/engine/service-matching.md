# Service Matching Algorithm

> **Version:** 1.0  
> **Updated:** December 12, 2025  
> **Purpose:** Programmatically detect article vertical/genre and match Trending Society Digital Services Division offerings

---

## Overview

This module defines the algorithm for automatically detecting article topics and matching the most relevant Trending Society service offerings. The goal is **contextual monetization** — positioning services that genuinely help the reader based on what they're reading about.

---

## Detection Algorithm

### Step 1: Vertical Classification

Analyze article content for vertical signals:

```
INPUT: Article title, content, source URL, tags
OUTPUT: Primary vertical (1) + Secondary verticals (0-2)
```

### Vertical Detection Signals

| Vertical | Primary Keywords | Entity Signals | Source Signals |
|----------|-----------------|----------------|----------------|
| **Tech** | AI, API, software, SaaS, startup, developer, code | OpenAI, Google, Microsoft, NVIDIA | TechCrunch, Verge, Wired |
| **Sports** | game, team, player, score, championship, athlete | ESPN, NFL, NBA, FIFA | ESPN, Bleacher Report |
| **Finance** | stock, market, trading, investment, crypto, bank | S&P, NASDAQ, Fed, SEC | Bloomberg, CNBC, WSJ |
| **Entertainment** | movie, show, celebrity, streaming, music, album | Netflix, Spotify, Disney | Variety, Hollywood Reporter |
| **Real Estate** | property, mortgage, home, listing, realtor | Zillow, Redfin, NAR | Realtor.com, Curbed |
| **Gaming** | game, console, esports, streaming, PC, mobile | Steam, PlayStation, Xbox | IGN, Kotaku, Polygon |
| **Health** | wellness, fitness, medical, supplement, diet | FDA, CDC, WHO | WebMD, Healthline |
| **Business** | company, revenue, management, strategy, growth | Fortune 500, LinkedIn | Forbes, Inc, Fast Company |
| **Job Board** | hiring, career, salary, remote, job, interview | LinkedIn, Indeed | Glassdoor, LinkedIn News |
| **Music** | artist, album, tour, streaming, concert | Spotify, Billboard, Grammy | Pitchfork, Billboard |
| **World News** | government, policy, international, election | UN, EU, governments | Reuters, AP, BBC |
| **Art** | gallery, artist, exhibition, NFT, design | MoMA, Christie's, Sotheby's | Artnet, Artsy |
| **Lifestyle** | travel, fashion, food, home, wellness | Vogue, Condé Nast | Eater, Bon Appétit |

---

### Step 2: Topic Extraction

Extract specific topic signals within the vertical:

```json
{
  "vertical": "tech",
  "topics": [
    {"topic": "ai_agents", "confidence": 0.92},
    {"topic": "automation", "confidence": 0.85},
    {"topic": "saas", "confidence": 0.45}
  ]
}
```

### Topic Keywords by Vertical

<details>
<summary><strong>Tech Topics</strong></summary>

| Topic ID | Keywords | Service Match |
|----------|----------|---------------|
| `ai_agents` | GPT, LLM, copilot, agent, ChatGPT, Claude, AI assistant | Custom GPT Development |
| `automation` | workflow, n8n, Zapier, automate, integrate, API | Business Automation Workflows |
| `ecommerce` | Shopify, store, product, commerce, checkout | Social Commerce & Shopify |
| `seo_content` | SEO, search, ranking, traffic, organic | SEO & GEO Optimization |
| `social_media` | distribution, posting, scheduling, content | Social Media Automation |
| `software_dev` | build, code, app, platform, infrastructure | Custom Software Builds |
| `saas` | subscription, recurring, platform, product | SaaS Apps |
| `branding` | logo, identity, rebrand, design system | Logo & Brand Design |

</details>

<details>
<summary><strong>Business Topics</strong></summary>

| Topic ID | Keywords | Service Match |
|----------|----------|---------------|
| `ops_efficiency` | operations, efficiency, process, workflow | Business Automation Workflows |
| `digital_transform` | digital, modernize, tech stack, tools | Custom Software Builds |
| `marketing` | marketing, brand, campaigns, awareness | Social Media Automation |
| `startup` | startup, funding, launch, MVP, product | SaaS Apps |
| `local_business` | local, location, maps, discovery | SEO & GEO Optimization |

</details>

<details>
<summary><strong>Entertainment/Creator Topics</strong></summary>

| Topic ID | Keywords | Service Match |
|----------|----------|---------------|
| `creator_economy` | creator, influencer, content, monetize | Social Commerce & Shopify |
| `merch` | merchandise, store, products, fans | Social Commerce & Shopify |
| `distribution` | clips, shorts, repurpose, cross-post | Social Media Automation |
| `personal_brand` | personal brand, identity, presence | Logo & Brand Design |

</details>

---

### Step 3: Service Matching Matrix

Cross-reference vertical + topics to determine optimal service:

```
ALGORITHM:
1. Get primary vertical
2. Extract top 3 topics with confidence > 0.5
3. Match topic → service using Topic Keywords table
4. If multiple services match, rank by:
   a) Topic confidence score
   b) Vertical-service affinity (see matrix below)
   c) Recent conversion data (if available)
5. Select primary service (always) + secondary service (if confidence > 0.7)
```

### Vertical-Service Affinity Matrix

| Vertical | Primary Service | Secondary Service | Tertiary Service |
|----------|----------------|-------------------|------------------|
| **Tech** | Custom GPT Development | Business Automation | Custom Software |
| **Sports** | Social Media Automation | SEO & GEO | — |
| **Finance** | Custom Software | Business Automation | SEO & GEO |
| **Entertainment** | Social Commerce & Shopify | Social Media Automation | Logo & Brand |
| **Real Estate** | SEO & GEO | Custom Software | — |
| **Gaming** | Social Media Automation | Custom GPT Development | — |
| **Health** | SEO & GEO | Social Media Automation | — |
| **Business** | Business Automation | Custom Software | SaaS Apps |
| **Job Board** | SEO & GEO | Social Media Automation | — |
| **Music** | Social Commerce & Shopify | Social Media Automation | — |
| **World News** | SEO & GEO | Social Media Automation | — |
| **Art** | Social Commerce & Shopify | Logo & Brand | — |
| **Lifestyle** | Social Commerce & Shopify | Social Media Automation | SEO & GEO |

---

## Service Definitions

### Digital Services Division Menu

| ID | Service | URL | Best For |
|----|---------|-----|----------|
| 1 | Social Commerce & Shopify Builds | `/services/social-commerce-shopify-builds` | Creators, e-commerce, community commerce |
| 2 | Business Automation Workflows | `/services/business-automation-workflows` | Ops efficiency, workflow automation |
| 3 | Logo & Brand Design | `/services/logo-brand-design` | Visual identity, rebranding |
| 4 | SEO & GEO Optimization | `/services/seo-geo-optimization` | Search visibility, local discovery |
| 5 | Custom Software Builds | `/services/custom-software-builds` | Technical solutions, infrastructure |
| 6 | Custom GPT Development | `/services/custom-gpt-development` | AI agents, LLMs, copilots |
| 7 | Social Media Automation | `/services/social-media-automation` | Content distribution, scheduling |
| 8 | SaaS Apps | `/services/saas-apps` | Product development, MVPs |

---

## CTA Generation Rules

### Template by Match Type

**High Confidence Match (>0.85):**
```markdown
**Ready to [action verb] for your [industry/role]?** 
Trending Society's [Service Name] helps [specific outcome]. 
[Learn more →](/services/[service-slug])
```

**Medium Confidence Match (0.6-0.85):**
```markdown
**Exploring [topic]?** 
Our [Service Name] team can help you [benefit]. 
[Get in touch →](/services/[service-slug])
```

**Low Confidence / Default:**
```markdown
**Need help with [general area]?** 
Trending Society offers [Service Name] for [broad audience]. 
[See how →](/services/[service-slug])
```

---

## Implementation (n8n)

### Detection Node (Code)

```javascript
// Extract vertical and topics from article
function detectVerticalAndTopics(article) {
  const verticals = {
    tech: ['ai', 'api', 'software', 'saas', 'startup', 'developer'],
    sports: ['game', 'team', 'player', 'score', 'championship'],
    finance: ['stock', 'market', 'trading', 'investment', 'crypto'],
    entertainment: ['movie', 'show', 'celebrity', 'streaming'],
    business: ['company', 'revenue', 'management', 'strategy'],
    // ... other verticals
  };

  const content = (article.title + ' ' + article.content).toLowerCase();
  const scores = {};

  for (const [vertical, keywords] of Object.entries(verticals)) {
    scores[vertical] = keywords.filter(kw => content.includes(kw)).length;
  }

  const sorted = Object.entries(scores).sort((a, b) => b[1] - a[1]);
  return {
    primary: sorted[0][0],
    secondary: sorted[1]?.[1] > 0 ? sorted[1][0] : null,
    confidence: sorted[0][1] / 5 // Normalize to 0-1
  };
}
```

### Service Matching Node

```javascript
// Match vertical to service
function matchService(vertical, topics) {
  const affinityMatrix = {
    tech: ['custom-gpt-development', 'business-automation-workflows'],
    sports: ['social-media-automation', 'seo-geo-optimization'],
    finance: ['custom-software-builds', 'business-automation-workflows'],
    entertainment: ['social-commerce-shopify-builds', 'social-media-automation'],
    business: ['business-automation-workflows', 'custom-software-builds'],
    // ... other mappings
  };

  const services = affinityMatrix[vertical] || ['seo-geo-optimization'];
  
  return {
    primary: services[0],
    secondary: services[1] || null,
    url: `/services/${services[0]}`
  };
}
```

---

## Quality Metrics

Track these to optimize the algorithm:

| Metric | Target | Purpose |
|--------|--------|---------|
| Vertical accuracy | >90% | Correct classification |
| Service click-through | >2% | CTA relevance |
| Service inquiry rate | >0.5% | Conversion quality |
| Reader satisfaction | >4.0/5 | Non-intrusive positioning |

---

## Examples

### Example 1: Tech/AI Article

**Article:** "OpenAI Launches GPT-5 with Agent Capabilities"

```json
{
  "vertical": "tech",
  "topics": ["ai_agents", "automation"],
  "service_match": {
    "primary": "Custom GPT Development",
    "url": "/services/custom-gpt-development",
    "confidence": 0.95
  },
  "cta": "Ready to build custom AI agents for your business? Trending Society's Custom GPT Development team can help you leverage the latest GPT technology."
}
```

### Example 2: Entertainment/Creator Article

**Article:** "Taylor Swift's Eras Tour Merch Sells $200M"

```json
{
  "vertical": "entertainment",
  "topics": ["creator_economy", "merch"],
  "service_match": {
    "primary": "Social Commerce & Shopify Builds",
    "url": "/services/social-commerce-shopify-builds",
    "confidence": 0.88
  },
  "cta": "Building a merch empire? Our Social Commerce & Shopify team helps creators launch stores that sell."
}
```

### Example 3: Business/Ops Article

**Article:** "How Companies Are Automating Manual Workflows"

```json
{
  "vertical": "business",
  "topics": ["ops_efficiency", "automation"],
  "service_match": {
    "primary": "Business Automation Workflows",
    "url": "/services/business-automation-workflows",
    "confidence": 0.92
  },
  "cta": "Ready to eliminate manual busywork? Our Business Automation team builds workflows that actually work."
}
```

---

## Integration Points

- **Master Prompt (v5.2):** Reference this module for CTA generation
- **n8n Workflow:** Implement detection and matching nodes
- **Airtable:** Store service match data for analytics
- **Quality Gate:** Verify CTA matches article topic

---

_Algorithm maintained by Trending Society Digital Services Division_
