# WordPress Platform Adapter

> **Version:** 1.0  
> **Updated:** December 12, 2025  
> **Platform:** WordPress  
> **API:** REST API v2  
> **Status:** 🔲 Ready

---

## Overview

Transforms core engine output into WordPress Post format for publishing via the REST API.

---

## API Reference

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/wp-json/wp/v2/posts` | POST | Create post |
| `/wp-json/wp/v2/posts/{id}` | PUT | Update post |

**Documentation:** [WordPress REST API](https://developer.wordpress.org/rest-api/reference/posts/)

---

## Field Mapping

| Core Engine Field | WordPress Field | Notes |
|-------------------|-----------------|-------|
| `title` | `title` | Direct mapping |
| `content_html` | `content` | Direct mapping |
| `excerpt` | `excerpt` | Direct mapping |
| `seo_title` | `yoast_meta[title]` | Via Yoast SEO plugin |
| `meta_description` | `yoast_meta[description]` | Via Yoast SEO plugin |
| `slug` | `slug` | Direct mapping |
| `tags` | `tags` | Array of tag IDs |
| `schema_json` | Injected in `content` | As `<script type="application/ld+json">` |
| `author` | `author` | Author ID (integer) |
| `published_at` | `date` | ISO 8601 format |

---

## API Payload Format

```json
{
  "title": "{{title}}",
  "content": "{{content_html}}\n<!-- wp:html -->\n<script type=\"application/ld+json\">{{schema_json}}</script>\n<!-- /wp:html -->",
  "excerpt": "{{excerpt}}",
  "slug": "{{slug}}",
  "status": "publish",
  "author": {{author_id}},
  "tags": [{{tag_ids}}],
  "meta": {
    "_yoast_wpseo_title": "{{seo_title}}",
    "_yoast_wpseo_metadesc": "{{meta_description}}"
  }
}
```

---

## Authentication Options

### 1. Application Passwords (Recommended)
```
Authorization: Basic base64(username:application_password)
```

### 2. JWT Authentication (Plugin Required)
```
Authorization: Bearer {{jwt_token}}
```

### 3. OAuth 2.0 (Enterprise)
```
Authorization: Bearer {{oauth_token}}
```

---

## Yoast SEO Integration

If Yoast SEO is installed, use these meta fields:

| Field | Meta Key | Purpose |
|-------|----------|---------|
| SEO Title | `_yoast_wpseo_title` | Custom page title |
| Meta Description | `_yoast_wpseo_metadesc` | Meta description |
| Focus Keyword | `_yoast_wpseo_focuskw` | Primary keyword |
| Canonical URL | `_yoast_wpseo_canonical` | Canonical link |

---

## Tag Management

WordPress requires tag IDs, not names. Use this flow:

1. **Check if tag exists:** `GET /wp-json/wp/v2/tags?search={{tag_name}}`
2. **Create if not exists:** `POST /wp-json/wp/v2/tags` with `{"name": "{{tag_name}}"}`
3. **Use tag ID** in post creation

---

## n8n Integration

### HTTP Request Node Configuration

```json
{
  "method": "POST",
  "url": "https://{{domain}}/wp-json/wp/v2/posts",
  "authentication": "basicAuth",
  "credentials": {
    "username": "{{wp_username}}",
    "password": "{{application_password}}"
  },
  "headers": {
    "Content-Type": "application/json"
  },
  "body": "{{wordpress_payload}}"
}
```

---

## Error Handling

| Error Code | Meaning | Resolution |
|------------|---------|------------|
| 401 | Unauthorized | Check credentials |
| 403 | Forbidden | Check user permissions |
| 400 | Bad request | Validate payload |
| 404 | Endpoint not found | Check permalink settings |

---

## Gutenberg Blocks

For Gutenberg compatibility, wrap custom HTML:

```html
<!-- wp:html -->
<div class="source-badge">...</div>
<!-- /wp:html -->
```

---

*Adapter maintained by Trending Society*
