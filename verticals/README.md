# Vertical Templates

> **Version:** 1.0  
> **Updated:** December 12, 2025  
> **Purpose:** Industry-specific prompt overrides and entity registries

---

## Overview

Vertical templates customize the core engine for specific industries, providing:

- Industry-specific voice and tone adjustments
- Curated entity registries (key people, companies, terms)
- Specialized source registries
- Vertical-appropriate CTA matching

---

## Supported Verticals

| Vertical      | Status     | Folder           | Primary Affiliates               |
| ------------- | ---------- | ---------------- | -------------------------------- |
| Tech          | ✅ Base    | `tech/`          | SaaS, hardware, dev tools        |
| Sports        | 🔲 Ready   | `sports/`        | Betting, tickets, memorabilia    |
| Finance       | 🔲 Ready   | `finance/`       | Trading, credit cards, insurance |
| Entertainment | 🔲 Ready   | `entertainment/` | Streaming, tickets, merch        |
| Real Estate   | 🔲 Planned | `real-estate/`   | Mortgages, home services         |
| Gaming        | 🔲 Planned | `gaming/`        | Game sales, hardware, subs       |
| Health        | 🔲 Planned | `health/`        | Supplements, telehealth          |
| Business      | 🔲 Planned | `business/`      | SaaS tools, courses              |
| Job Board     | 🔲 Planned | `job-board/`     | Job postings, resume services    |
| Music         | 🔲 Planned | `music/`         | Streaming, concert tickets       |
| World News    | 🔲 Planned | `world-news/`    | VPN, news subs                   |
| Art           | 🔲 Planned | `art/`           | Prints, courses, supplies        |
| Lifestyle     | 🔲 Planned | `lifestyle/`     | E-commerce, travel               |

---

## Vertical Structure

Each vertical folder contains:

```
verticals/{vertical}/
├── prompt-override.md     # Voice, tone, and style adjustments
├── entity-registry.md     # Key people, companies, terms
├── source-registry.md     # Trusted sources for this vertical
└── affiliate-config.md    # Affiliate integration settings
```

---

## Using Vertical Templates

### In n8n Workflow

1. Detect vertical from source content
2. Load vertical-specific prompt override
3. Merge with core master prompt
4. Generate content with vertical context

### Prompt Override Format

```markdown
## Vertical: {Name}

### Voice Adjustments

- [Specific tone modifications]

### Entity Focus

- [Key entities to emphasize]

### Source Preferences

- [Preferred sources for this vertical]

### Affiliate Opportunities

- [CTA and monetization guidance]
```

---

## Creating New Verticals

1. Create folder: `verticals/{vertical-name}/`
2. Create `prompt-override.md` with voice adjustments
3. Create `entity-registry.md` with key entities
4. Copy relevant sources from `shared/source-registry.md`
5. Define affiliate opportunities
6. Update this README with status

---

_Built by Trending Society_
