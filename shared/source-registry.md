# Trusted External Source Registry

> **Purpose:** Authoritative sources for external keyword links  
> **Updated:** December 2025

Use these authoritative sources for external keyword links. Include logo URL for badge display.

| Source Name           | Domain               | Logo URL (for badge)                                                                           | Topics                       |
| --------------------- | -------------------- | ---------------------------------------------------------------------------------------------- | ---------------------------- |
| TechCrunch            | techcrunch.com       | `https://techcrunch.com/wp-content/uploads/2024/01/cropped-cropped-favicon-256x256-1.png`      | Tech news, startups, funding |
| The Verge             | theverge.com         | `https://www.google.com/s2/favicons?domain=theverge.com&sz=64`                                 | `https://cdn.vox-cdn.com/uploads/chorus_asset/file/7395367/favicon-64x64.0.png` | Consumer tech, gadgets       | Dec 2025      |
| Wired                 | wired.com            | `https://www.google.com/s2/favicons?domain=wired.com&sz=64`                                    | `https://www.wired.com/verso/static/wired/assets/favicon.ico`    | Tech culture, science        | Dec 2025      |
| Bloomberg             | bloomberg.com        | `https://www.google.com/s2/favicons?domain=bloomberg.com&sz=64`                                | `https://assets.bwbx.io/s3/javelin/public/hub/images/favicon-black-63fe5249d3.png` | Business, finance  | Dec 2025      |
| Reuters               | reuters.com          | `https://www.google.com/s2/favicons?domain=reuters.com&sz=64`                                  | `https://www.reuters.com/pf/resources/images/reuters/favicon.ico` | Breaking news, global        | Dec 2025      |
| WSJ                   | wsj.com              | `https://www.google.com/s2/favicons?domain=wsj.com&sz=64`                                      | `https://s.wsj.net/favicon.ico`                                   | Business, markets            | Dec 2025      |
| NYT                   | nytimes.com          | `https://www.google.com/s2/favicons?domain=nytimes.com&sz=64`                                  | `https://www.nytimes.com/vi-assets/static-assets/favicon-4bf96cb6a1093748bf5b3c429accb9b4.ico` | News, tech, business | Dec 2025 |
| Forbes                | forbes.com           | `https://www.google.com/s2/favicons?domain=forbes.com&sz=64`                                   | `https://i.forbesimg.com/48X48-F.png`                             | Business, entrepreneurs      | Dec 2025      |
| VentureBeat           | venturebeat.com      | `https://www.google.com/s2/favicons?domain=venturebeat.com&sz=64`                              | `https://venturebeat.com/wp-content/themes/flavor-flavor-flavor-flavor/flavor/favicon.ico` | AI, enterprise tech | Dec 2025 |
| Ars Technica          | arstechnica.com      | `https://www.google.com/s2/favicons?domain=arstechnica.com&sz=64`                              | `https://cdn.arstechnica.net/favicon.ico`                         | Deep tech analysis           | Dec 2025      |
| MIT Technology Review | technologyreview.com | `https://www.google.com/s2/favicons?domain=technologyreview.com&sz=64`                         | `https://www.technologyreview.com/favicon.ico`                    | Emerging tech, research      | Dec 2025      |
| OpenAI                | openai.com           | `https://www.google.com/s2/favicons?domain=openai.com&sz=64`                                   | `https://openai.com/favicon.ico`                                  | AI research, products        | Dec 2025      |
| Google                | google.com           | `https://www.google.com/s2/favicons?domain=google.com&sz=64`                                   | `https://www.google.com/favicon.ico`                              | Search, AI, cloud            | Dec 2025      |
| Microsoft             | microsoft.com        | `https://www.google.com/s2/favicons?domain=microsoft.com&sz=64`                                | `https://c.s-microsoft.com/favicon.ico`                           | Enterprise, AI, cloud        | Dec 2025      |
| Amazon/AWS            | aws.amazon.com       | `https://www.google.com/s2/favicons?domain=aws.amazon.com&sz=64`                               | `https://a0.awsstatic.com/libra-css/images/site/fav/favicon.ico` | Cloud, AI services           | Dec 2025      |

## Usage

When linking to external sources in content:
1. Use the source name as the hyperlink text
2. Use logo fallback chain for reliability (Primary → Fallback → Default)
3. Link to the specific article/page, not the homepage
4. Prefer authoritative sources from this registry

### Logo Fallback Chain (Circuit Breaker Pattern)

**Always use this pattern to prevent broken images:**

```javascript
async function getSourceLogo(sourceName) {
  const source = SOURCE_REGISTRY.find(s => s.name === sourceName);
  
  // 1. Try Primary (Google Favicons - most reliable)
  try {
    const response = await fetch(source.logo_url_primary, { method: 'HEAD', timeout: 3000 });
    if (response.ok) return source.logo_url_primary;
  } catch (error) {
    console.log(`Primary logo failed for ${sourceName}`);
  }
  
  // 2. Try Fallback (Original source URL)
  try {
    const response = await fetch(source.logo_url_fallback, { method: 'HEAD', timeout: 3000 });
    if (response.ok) return source.logo_url_fallback;
  } catch (error) {
    console.log(`Fallback logo failed for ${sourceName}`);
  }
  
  // 3. Use Default
  console.log(`All logos failed for ${sourceName}, using default`);
  return 'https://res.cloudinary.com/trending-society/image/upload/v1/default-source-logo.png';
}
```

**In HTML Output:**

```html
<span class="source-badge">
  <img 
    src="[PRIMARY_LOGO_URL]" 
    onerror="this.onerror=null; this.src='[FALLBACK_LOGO_URL]';"
    alt="[Source Name] logo" 
    class="source-logo"
  >
  <a href="[SOURCE_URL]" target="_blank" rel="noopener">[Source Name]</a>
</span>
```

This ensures images never break in published content.

### Why Google Favicons as Primary?

Google's favicon service (`https://www.google.com/s2/favicons?domain=example.com&sz=64`) is:
- **Highly reliable** (99.9%+ uptime)
- **Automatically updated** when sites change favicons
- **Cached globally**
- **Fast** (CDN-delivered)
- **Free** (no API key required)

Original URLs kept as fallback for higher quality when available.

---

**See also:** `core/circuit-breakers.md` for complete implementation guide

