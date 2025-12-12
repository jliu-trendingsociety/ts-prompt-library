# Shopify Platform Adapter

> **Version:** 1.0  
> **Updated:** December 12, 2025  
> **Platform:** Shopify  
> **API Version:** 2024-10 (stable)  
> **Status:** ✅ Primary

---

## Overview

Transforms core engine output into Shopify Blog Article format for publishing via the Admin API.

---

## API Reference

| Endpoint                                                | Method | Purpose        |
| ------------------------------------------------------- | ------ | -------------- |
| `/admin/api/2024-10/blogs/{blog_id}/articles.json`      | POST   | Create article |
| `/admin/api/2024-10/blogs/{blog_id}/articles/{id}.json` | PUT    | Update article |

**Documentation:** [Shopify Blog Articles API](https://shopify.dev/docs/api/admin-rest/2024-10/resources/article)

---

## Field Mapping

| Core Engine Field  | Shopify Field                 | Notes                                    |
| ------------------ | ----------------------------- | ---------------------------------------- |
| `title`            | `title`                       | Direct mapping                           |
| `content_html`     | `body_html`                   | Direct mapping                           |
| `excerpt`          | `summary_html`                | Direct mapping                           |
| `seo_title`        | `metafields[seo_title]`       | Via metafield                            |
| `meta_description` | `metafields[seo_description]` | Via metafield                            |
| `slug`             | `handle`                      | Direct mapping                           |
| `tags`             | `tags`                        | Comma-separated string                   |
| `schema_json`      | Injected in `body_html`       | As `<script type="application/ld+json">` |
| `author`           | `author`                      | Author name string                       |
| `published_at`     | `published_at`                | ISO 8601 format                          |

---

## API Payload Format

```json
{
  "article": {
    "title": "{{title}}",
    "author": "{{author.name}}",
    "tags": "{{tags|join:','}}",
    "body_html": "{{content_html}}\n<script type=\"application/ld+json\">{{schema_json}}</script>",
    "summary_html": "{{excerpt}}",
    "handle": "{{slug}}",
    "published_at": "{{published_at}}",
    "metafields": [
      {
        "namespace": "seo",
        "key": "title",
        "value": "{{seo_title}}",
        "type": "single_line_text_field"
      },
      {
        "namespace": "seo",
        "key": "description",
        "value": "{{meta_description}}",
        "type": "single_line_text_field"
      }
    ]
  }
}
```

---

## Schema Injection

The JSON-LD schema should be injected at the end of `body_html`:

```html
{{content_html}}

<script type="application/ld+json">
  {{schema_json}}
</script>
```

---

## CSS Injection

Source badge CSS should be added to the Shopify theme:

**Location:** `Assets/base.css` or `Snippets/custom-styles.liquid`

See: `editorial/v5/master-prompt.md` → Section 10: CSS Styles

---

## n8n Integration

### HTTP Request Node Configuration

```json
{
  "method": "POST",
  "url": "https://{{shop}}.myshopify.com/admin/api/2024-10/blogs/{{blog_id}}/articles.json",
  "authentication": "headerAuth",
  "headers": {
    "X-Shopify-Access-Token": "{{access_token}}",
    "Content-Type": "application/json"
  },
  "body": "{{shopify_payload}}"
}
```

---

## Error Handling

| Error Code | Meaning          | Resolution            |
| ---------- | ---------------- | --------------------- |
| 401        | Unauthorized     | Check access token    |
| 404        | Blog not found   | Verify blog_id        |
| 422        | Validation error | Check required fields |
| 429        | Rate limited     | Implement backoff     |

---

## Rate Limits

- **Standard:** 2 requests/second
- **Plus:** 4 requests/second
- **Recommended:** Use n8n rate limiting

---

_Adapter maintained by Trending Society_
