# Editorial Prompts

> **Purpose:** Blog posts, articles, and SEO-optimized content for Shopify

## Files

### Current Version
- **`v5/master-prompt.md`** - Latest SEO/AEO optimized format
  - Keyword hyperlinking protocol
  - Internal linking system
  - Branded source citations
  - 10 structured blocks for Shopify

### Legacy Versions
- **`v4/master-prompt-video-image.md`** - Version with media generation
  - Includes AI image/video prompt generation
  - Media extraction protocol
  - 11 structured blocks

## Usage

**For standard blog posts:**
```
Use: editorial/v5/master-prompt.md
Output: 10-block Shopify format
```

**For posts with media generation:**
```
Use: editorial/v4/master-prompt-video-image.md
Output: 11-block format with image/video prompts
```

## Output Format

Both versions output structured blocks ready for Shopify:
1. Title & Meta
2. Excerpt
3. Body Content
4. Tags
5. SEO Fields
6. Schema Markup
7. Citations
8. Internal Links
9. CTA
10. CSS Styles (v5) or Media Prompts (v4)

## Dependencies

- `shared/article-index.md` - For internal linking
- `shared/source-registry.md` - For external citations
- `shared/service-menu.md` - For CTAs

## Version History

- **v5** (Current) - Added keyword hyperlinking, branded citations
- **v4** - Added media generation, video/image prompts

