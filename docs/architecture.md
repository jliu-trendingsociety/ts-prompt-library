# Content Engine Architecture

> **Version:** 1.0  
> **Updated:** December 12, 2025  
> **Status:** v6.0 Multi-Platform Architecture

---

## System Overview

The Content Engine is a modular, multi-platform content generation system that transforms source articles into SEO/AEO-optimized content ready for publishing across multiple CMS platforms.

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              USER INTERFACES                                 │
├─────────────────────────────────────────────────────────────────────────────┤
│  Airtable Forms → Airtable Views → Softr Portal → Custom Frontend          │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                           AIRTABLE DATA LAYER                                │
│  Requests │ Articles │ Enrichments │ Clients │ Platform_Connections         │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼ Webhooks
┌─────────────────────────────────────────────────────────────────────────────┐
│                              n8n LOGIC LAYER                                 │
│  Article Pipeline │ Publishing │ Enrichments │ RSS Ingestion                │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
           ┌────────────────────────┼────────────────────────┐
           ▼                        ▼                        ▼
┌─────────────────┐      ┌─────────────────┐      ┌─────────────────┐
│   CORE ENGINE   │      │    PLATFORM     │      │   ENRICHMENT    │
│                 │      │    ADAPTERS     │      │   SERVICES      │
│  Master Prompt  │      │  Shopify        │      │  ElevenLabs     │
│  Schema Gen     │      │  WordPress      │      │  HeyGen         │
│  Quality Gate   │      │  Webflow        │      │  Decart         │
│  Verticals      │      │  Next.js        │      │  Social APIs    │
└─────────────────┘      └─────────────────┘      └─────────────────┘
```

---

## Core Components

### 1. Core Engine (`/editorial/v5/`)
The brain of the system — AI prompts that generate optimized content.

| Component | File | Purpose |
|-----------|------|---------|
| Master Prompt | `master-prompt.md` | Main content generation |
| Master Prompt Lite | `master-prompt-lite.md` | Cost-optimized version |
| Author Registry | `shared/author-registry.md` | E-E-A-T author profiles |
| Article Index | `shared/article-index.md` | Internal linking reference |
| Source Registry | `shared/source-registry.md` | Trusted external sources |

### 2. Schema Templates (`/core/schemas/`)
JSON-LD templates for structured data.

| Schema | File | Use Case |
|--------|------|----------|
| Article | `article.schema.json` | Evergreen content |
| NewsArticle | `newsarticle.schema.json` | Timely/breaking news |
| VideoObject | `video.schema.json` | AI-generated videos |
| PodcastEpisode | `podcast.schema.json` | AI podcasts |

### 3. Platform Adapters (`/platforms/`)
Transform core output into CMS-specific formats.

| Platform | Adapter | Status |
|----------|---------|--------|
| Shopify | `shopify/adapter.md` | ✅ Primary |
| WordPress | `wordpress/adapter.md` | 🔲 Ready |
| Webflow | `webflow/adapter.md` | 🔲 Planned |
| Next.js | `nextjs/adapter.md` | 🔲 Planned |

### 4. Vertical Templates (`/verticals/`)
Industry-specific prompt overrides.

| Vertical | Status | Key Focus |
|----------|--------|-----------|
| Tech | ✅ Base | AI, software, startups |
| Sports | 🔲 Planned | Athletes, betting, teams |
| Finance | 🔲 Planned | Markets, trading, crypto |
| Entertainment | 🔲 Planned | Movies, celebrities, streaming |

---

## Data Flow

### Content Generation Flow
```
1. Request Created (Airtable)
   ↓
2. Webhook triggers n8n workflow
   ↓
3. Source content fetched and parsed
   ↓
4. Vertical detected (AI classification)
   ↓
5. Master Prompt generates content
   ↓
6. Schema JSON generated from templates
   ↓
7. Quality gate validates output
   ↓
8. Article saved to Airtable
   ↓
9. Enrichments triggered (if enabled)
```

### Publishing Flow
```
1. Article approved in Airtable
   ↓
2. Webhook triggers publishing workflow
   ↓
3. Platform adapter formats content
   ↓
4. API call to target CMS
   ↓
5. Published URL saved to Airtable
```

---

## Scaling Strategy

### Phase 1: Validate (Current)
- n8n (self-hosted) + Airtable Pro
- 100-500 articles/month
- 5-20 users

### Phase 2: Early Revenue
- Add Softr/Stacker portal
- 500-2,000 articles/month
- 20-100 clients

### Phase 3: Scale
- Add custom API layer
- Redis cache, Postgres mirror
- 2,000-10,000 articles/month

### Phase 4: Full SaaS
- Custom backend (Node/Python)
- Multi-tenant architecture
- 10,000+ articles/month

---

## Key Design Decisions

### Why n8n?
- Visual workflow builder for rapid iteration
- Self-hostable for data control
- Extensive API integrations
- Easy debugging and monitoring

### Why Airtable?
- Flexible schema for rapid changes
- Built-in UI for operations
- Native forms and views
- API for automation

### Why Modular Prompts?
- Vertical-specific customization
- Platform-agnostic core
- Version control and testing
- Team collaboration

---

## Security Considerations

### Credentials
- API keys stored in n8n credentials
- Airtable encryption for sensitive data
- Environment variables for config

### Access Control
- Airtable permissions per table
- n8n workflow permissions
- API key scoping (future)

---

## Related Documentation

- [n8n Workflows](./n8n-workflows.md)
- [Airtable Schema](./airtable-schema.md)
- [API Reference](../api/README.md)
- [Platform Adapters](../platforms/README.md)
- [Vertical Templates](../verticals/README.md)

---

*Architecture designed by Trending Society*
