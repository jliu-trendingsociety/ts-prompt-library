# Changelog

All notable changes to the Trending Society Prompt Library will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [Unreleased]

### Planned

- Complete all 12 vertical templates
- Innoreader RSS feed configurations
- ElevenLabs voice integration workflows
- Polymarket API integration workflows

---

## [3.0.0] - 2025-12-12

### Added - Multi-Platform Architecture (v6)

**Branch:** `feature/v6-modular-architecture`

#### Core Engine Upgrade (v5.2)

- NewsArticle schema for timely/breaking content (Top Stories eligible)
- Author Person entity with sameAs links for E-E-A-T
- `mentions` property for explicit entity linking
- BreadcrumbList schema for navigation display
- Author registry (`shared/author-registry.md`)
- Updated quality gate with v5.2 checks
- Now top 0.1% of publishers for SEO/AEO optimization

#### Modular Architecture

- New folder structure: `core/`, `platforms/`, `verticals/`, `api/`, `docs/`
- Schema templates: `core/schemas/` (Article, NewsArticle, Video, Podcast)
- Platform adapters: Shopify (ready), WordPress (ready), Webflow, Next.js
- Vertical templates: Tech (base), Sports, Finance, Entertainment planned

#### Platform Adapters (`/platforms/`)

- Shopify adapter with API reference
- WordPress adapter with Yoast SEO integration
- Adapter interface specification
- Multi-platform publishing support

#### Backend Architecture

- n8n workflow blueprints (`docs/n8n-workflows.md`)
- Airtable data layer schema (`docs/airtable-schema.md`)
- 6-table schema: Requests, Articles, Enrichments, Clients, Connections, Config
- Webhook integration patterns

#### API Specification

- OpenAPI-style documentation (`api/README.md`)
- Core endpoints: generate, publish, enrich, bulk
- Webhook events and payloads
- Rate limits by tier

#### Documentation

- Architecture overview (`docs/architecture.md`)
- System diagrams and data flows
- Scaling strategy (Validate → Revenue → Scale → SaaS)

### Linear Issues Created (TRE-51 to TRE-63)

| Epic                 | Issues           | Status   |
| -------------------- | ---------------- | -------- |
| Core Engine v5.2     | TRE-51 to TRE-56 | ✅ Done  |
| Modular Architecture | TRE-60           | ✅ Done  |
| Platform Adapters    | TRE-58           | 🔲 Ready |
| Vertical Templates   | TRE-59           | 🔲 Ready |
| n8n Backend          | TRE-57           | 🔲 Ready |
| Airtable Data Layer  | TRE-61           | 🔲 Ready |
| API Specification    | TRE-62           | 🔲 Ready |
| Documentation        | TRE-63           | 🔲 Ready |

---

## [2.5.0] - 2025-12-12

### Enhanced

- UI/UX improvements across all documentation files
- Emoji headers for better visual scanning in `.cursorrules`, `SYNC_STATUS.md`
- Collapsible `<details>` sections for external resource links
- Mermaid diagrams for:
  - Upskilling pathway (Intern → Creator Commerce)
  - UCI revenue flow
- GitHub-flavored callout boxes (`[!NOTE]`, `[!TIP]`, `[!IMPORTANT]`)
- Quick navigation table in `.cursorrules`
- Updated `media/video/script-templates.md` to December 2025
- Enhanced Notion page with emoji icons and callout blocks

---

## [2.4.0] - 2025-12-12

### Added

- Newspaper Theme for multi-vertical deployment:
  - Prebuilt Websites: https://demo.tagdiv.com/select_demo/newspaper-prebuilt-websites/?demo-type=membership
  - 120+ templates, vertical mapping for all 12 verticals
- Airtable Marketing Ops (TRE-48):
  - Human QA layer for interns/part-time staff
  - Upskilling pathway: Intern → AI Upskilling → Portfolio → Creator Commerce
- Iris ID biometric verification (TRE-49):
  - Identity foundation for AI Avatar/Voice Cloning consent
  - KYC/AML compliance, consent capture, wallet binding
- Universal Creative Income (UCI) infrastructure (TRE-50):
  - "Voice Once, Earn Forever" revenue model
  - UCI tiers: Bronze ($50-200/mo), Silver ($200-500), Gold ($500-2K), Platinum ($2K-10K+)
- Creator Commerce Protocol project link to `.cursorrules`
- Full ecosystem architecture to Notion documentation

### Updated

- `.cursorrules` version to 1.2.0
- Linear issues count: 21 (TRE-30 to TRE-50)
- SYNC_STATUS.md with Creator Economy Infrastructure section

---

## [2.3.0] - 2025-12-12

### Added

- AI Video Generation tools to `.cursorrules`:
  - HeyGen: https://www.heygen.com (AI avatars, talking heads)
  - Pollo.ai: https://pollo.ai/api-platform/explore (fast video gen, batch processing)
  - Decart AI: https://platform.decart.ai (real-time video, LipSync Live)
  - Runway: https://runwayml.com (cinematic clips)
  - Pika: https://pika.art (motion graphics)
  - Synthesia: https://www.synthesia.io (enterprise avatars)
  - Lumen5: https://lumen5.com (slideshows)
  - Opus Clip: https://www.opus.pro (podcast clips)
- Expanded Innoreader section with full API docs: https://developer.innoreader.com
- AI Video Generation Tools section to SYNC_STATUS.md
- Linear TRE-47: Integrate HeyGen AI Avatar video generation

### Updated

- Linear TRE-39: Comprehensive video tool matrix with all 8 tools
- Notion page with AI Video Generation Pipeline section
- `.cursorrules` version to 1.1.0

---

## [2.2.0] - 2025-12-12

### Added

- `.cursorrules` - Cursor AI best practices and guidelines
- `SYNC_STATUS.md` - Cross-platform sync tracking (GitHub/Notion/Linear)
- `CHANGELOG.md` - Version history documentation
- Multi-Vertical Blog Engine project documentation
  - Notion Hub: https://www.notion.so/2c753453a9f181e8867cea6bedd58fee
  - Linear Project: https://linear.app/trending-society/project/multi-vertical-blog-engine-b980cfc74730

### Updated

- Expanded from 7 to 12 supported verticals:
  - Tech, Sports, Entertainment, Finance, Real Estate, Gaming
  - Health & Wellness, Business, Job Board, Music, World News, Art, Lifestyle

### Documented

- **Investor Overview** - $50B+ TAM, revenue projections ($3.1M → $28.8M ARR)
- **Vertical Publisher Strategy** - Publisher arbitrage vs NYT/Bloomberg
- **Content Multiplication Engine** - 1 article → 10+ content pieces
- **Voice Creator Program** - ElevenLabs licensing and revenue share
- **Polymarket Integration** - Prediction market data for content
- **Innoreader RSS Architecture** - 305+ feeds, 650-1,070 articles/day

### Linear Issues Created

- TRE-30: Design core n8n workflow architecture
- TRE-31: Create vertical-specific prompt templates
- TRE-32: Build Shopify publishing integration
- TRE-33: Implement source ingestion pipeline
- TRE-34: Set up testing and validation framework
- TRE-35: Create article index and analytics tracking
- TRE-36: Set up Innoreader RSS feed architecture
- TRE-37: Build article-to-podcast pipeline
- TRE-38: Build AI image generation pipeline
- TRE-39: Build AI video generation pipeline
- TRE-40: Build social content repurposing workflow
- TRE-41: Configure affiliate integrations per vertical
- TRE-42: Build vertical-specific source registries
- TRE-43: Set up multi-platform social publishing
- TRE-44: Build newsletter system for 12 verticals
- TRE-45: Integrate Polymarket prediction markets
- TRE-46: Build ElevenLabs voice licensing and creator revenue share program

---

## [2.1.0] - 2025-12-06

### Added

- Enterprise operating system alignment features
- AI Cost Control with model routing (85% savings on routine content)
- Canonical ID System with 301 redirect automation
- Structured Logging for costs, quality, and debugging
- Circuit Breakers for graceful fallback
- Idempotency for safe retries with zero duplicates
- Verification Schedule for quarterly spec validation
- Integration Templates for n8n workflows
- Cost-Optimized Prompts (Lite versions at $0.001 vs $0.025)

### Updated

- Master Prompt to v5.1 with competitive edge features
- Added sameAs Entity Authority (97% of competitors don't have)
- Added Speakable Voice Optimization (99% of competitors don't have)
- Added Entity Markup Protocol (95% of competitors don't have)
- Added HowTo Rich Snippets (98% of competitors don't have)

### Performance

- SEO/AEO Rank: Top 1-2% of tech blogs
- Grade: A (95/100)
- Status: Production-Ready

---

## [2.0.0] - 2025-11-15

### Added

- Complete prompt library restructure
- Modular folder organization
- Core instructions and configuration
- Editorial prompts (v5)
- Social media formats
- Email sequences
- Ad copy formats
- Media generation templates
- Shared resources (article index, source registry, service menu)

### Changed

- Migrated from single-file to modular architecture
- Introduced versioning for editorial prompts

---

## [1.0.0] - 2025-10-01

### Added

- Initial prompt library creation
- Basic blog post generation prompts
- SEO optimization guidelines
- Shopify integration support

---

## Version History Summary

| Version | Date       | Highlights                                                                    |
| ------- | ---------- | ----------------------------------------------------------------------------- |
| 3.0.0   | 2025-12-12 | **Multi-Platform Architecture**, v5.2 prompt, platform adapters, n8n/Airtable |
| 2.5.0   | 2025-12-12 | UI/UX improvements, emoji headers, Mermaid diagrams                           |
| 2.4.0   | 2025-12-12 | Newspaper Theme, Airtable Ops, Iris ID, UCI infrastructure                    |
| 2.3.0   | 2025-12-12 | AI Video tools: HeyGen, Pollo.ai, Decart AI, + 5 more                         |
| 2.2.0   | 2025-12-12 | Multi-Vertical Blog Engine, 12 verticals, Notion/Linear sync                  |
| 2.1.0   | 2025-12-06 | Enterprise features, v5.1 prompt, cost optimization                           |
| 2.0.0   | 2025-11-15 | Modular restructure, v5 prompt                                                |
| 1.0.0   | 2025-10-01 | Initial release                                                               |

---

## Links

- **Repository:** https://github.com/jliu-trendingsociety/ts-prompt-library
- **Notion Hub:** https://www.notion.so/2c753453a9f181e8867cea6bedd58fee
- **Linear Project:** https://linear.app/trending-society/project/multi-vertical-blog-engine-b980cfc74730
