# Platform Adapters

> **Version:** 1.0  
> **Updated:** December 12, 2025  
> **Purpose:** Transform core engine output into platform-specific formats

---

## Overview

Platform adapters convert the standardized output from the core engine into CMS-specific formats ready for publishing.

```
Core Engine Output → Platform Adapter → CMS-Ready Content
```

---

## Supported Platforms

| Platform     | Status     | Adapter                   | API Docs                                                                              |
| ------------ | ---------- | ------------------------- | ------------------------------------------------------------------------------------- |
| Shopify      | ✅ Primary | `shopify/adapter.md`      | [Shopify Blog API](https://shopify.dev/docs/api/admin-rest/2024-10/resources/article) |
| WordPress    | 🔲 Ready   | `wordpress/adapter.md`    | [WP REST API](https://developer.wordpress.org/rest-api/)                              |
| Webflow      | 🔲 Ready   | `webflow/adapter.md`      | [Webflow CMS API](https://developers.webflow.com/)                                    |
| Next.js      | 🔲 Ready   | `nextjs/adapter.md`       | MDX + Frontmatter                                                                     |
| Generic HTML | 🔲 Ready   | `generic-html/adapter.md` | Any CMS                                                                               |

---

## Adapter Interface

Each adapter must implement the following structure:

### Input (Core Engine Output)

```json
{
  "title": "Article title",
  "content_html": "<article>...</article>",
  "excerpt": "Summary text",
  "seo_title": "SEO optimized title",
  "meta_description": "Meta description",
  "slug": "url-slug",
  "tags": ["tag1", "tag2"],
  "schema_json": { "@context": "..." },
  "author": { "name": "...", "url": "..." },
  "published_at": "2025-12-12T00:00:00Z"
}
```

### Output (Platform-Specific)

Each adapter transforms this into the platform's required format.

---

## Usage

1. Generate content using the core engine (master-prompt.md)
2. Select the appropriate platform adapter
3. Transform output using adapter rules
4. Publish via platform API or manual paste

---

## Adding New Adapters

To add support for a new platform:

1. Create folder: `platforms/{platform-name}/`
2. Create `adapter.md` with transformation rules
3. Create `api-reference.md` with API documentation
4. Update this README with status
5. Add platform to master-prompt.md Platform Adapters table

---

_Built by Trending Society_
