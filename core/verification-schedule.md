# Prompt Verification Schedule

> **Purpose:** Maintain accuracy by verifying specs against official documentation  
> **Rule Alignment:** Core Rule 3 - Documentation First  
> **Updated:** December 2025

---

## Why This Matters

**Never code from memory. Always verify against latest docs.**

Platform specs, API versions, and tool parameters change frequently. Outdated prompts generate content that breaks, fails quality gates, or performs poorly.

This schedule ensures prompts stay current.

---

## Verification Schedule

| Module/File                             | Check Frequency | Last Verified | Next Check Due | Priority |
| --------------------------------------- | --------------- | ------------- | -------------- | -------- |
| `social/formats/all-platforms.md`       | Monthly         | Dec 6, 2025   | Jan 6, 2026    | HIGH     |
| `media/images/prompt-templates.md`      | Monthly         | Dec 6, 2025   | Jan 6, 2026    | HIGH     |
| `editorial/v5/master-prompt.md`         | Quarterly       | Dec 6, 2025   | Mar 6, 2026    | MEDIUM   |
| `ads/copy-formats.md`                   | Monthly         | Dec 6, 2025   | Jan 6, 2026    | HIGH     |
| `email/sequences.md`                    | Quarterly       | Dec 6, 2025   | Mar 6, 2026    | LOW      |
| `shared/source-registry.md`             | Weekly          | Dec 6, 2025   | Dec 13, 2025   | CRITICAL |
| `shared/article-index.md`               | Weekly          | Dec 6, 2025   | Dec 13, 2025   | MEDIUM   |
| `core/model-routing.md`                 | Monthly         | Dec 6, 2025   | Jan 6, 2026    | HIGH     |

---

## What to Verify

### Social Platforms (`social/formats/all-platforms.md`)

**Check:**
- Character limits (feed vs. expanded view)
- Hashtag recommendations
- Link behavior (clickable, preview, restrictions)
- Premium/paid feature differences
- Algorithm preference changes

**Sources:**
- [LinkedIn Help](https://www.linkedin.com/help/)
- [Twitter/X Help](https://help.twitter.com/)
- [Instagram Help](https://help.instagram.com/)
- [Meta Business Help](https://www.facebook.com/business/help)

**How to check:**
1. Visit official help docs
2. Test post creation on each platform
3. Note any character count changes
4. Update specs in prompt
5. Update "Last Verified" date

---

### AI Image Tools (`media/images/prompt-templates.md`)

**Check:**
- Tool version numbers (Midjourney v6 → v7)
- Parameter syntax changes (`--v 6` → `--v 7`)
- New features (style presets, quality modes)
- Deprecated parameters
- Pricing changes

**Sources:**
- [Midjourney Docs](https://docs.midjourney.com/)
- [OpenAI DALL-E Docs](https://platform.openai.com/docs/guides/images)
- [Flux Docs](https://docs.flux.ai/)
- Release notes and changelogs

**How to check:**
1. Check official docs for version updates
2. Test prompts with new parameters
3. Compare output quality
4. Update parameter syntax
5. Note version in prompt header

---

### Shopify API (`editorial/v5/master-prompt.md`)

**Check:**
- API version (Shopify releases quarterly)
- Article/Blog field changes
- Schema markup updates
- Metafield structure changes
- Redirect API changes

**Sources:**
- [Shopify API Changelog](https://shopify.dev/docs/api/release-notes)
- [Shopify Blog API](https://shopify.dev/docs/api/admin-rest/2024-10/resources/blog)
- [Schema.org updates](https://schema.org/docs/releases.html)

**How to check:**
1. Review Shopify changelog quarterly
2. Test API calls with current version
3. Verify schema markup validates
4. Update version references
5. Document breaking changes

---

### External Source Registry (`shared/source-registry.md`)

**Check:** Logo URLs still load (weekly)

**Critical because:** Broken logo URLs cause editorial content generation to fail.

**How to check:**
```javascript
// Automated check (run weekly via n8n)
async function verifyLogoUrls() {
  const sources = await loadSourceRegistry();
  const broken = [];
  
  for (const source of sources) {
    try {
      const response = await fetch(source.logo_url, { method: 'HEAD' });
      if (!response.ok) {
        broken.push({
          source: source.name,
          logo_url: source.logo_url,
          status: response.status
        });
      }
    } catch (error) {
      broken.push({
        source: source.name,
        logo_url: source.logo_url,
        error: error.message
      });
    }
  }
  
  if (broken.length > 0) {
    await sendAlert('Logo URLs broken', broken);
  }
  
  return broken;
}
```

**Fix broken URLs:**
1. Find current favicon URL
2. Update source-registry.md
3. Or host logo on Cloudinary (see Circuit Breaker guide)

---

### AI Model Pricing (`core/model-routing.md`)

**Check:** Token pricing changes (monthly)

**Sources:**
- [OpenAI Pricing](https://openai.com/api/pricing/)
- [Anthropic Pricing](https://www.anthropic.com/pricing)
- [Google AI Pricing](https://ai.google.dev/pricing)

**How to check:**
1. Visit pricing pages monthly
2. Compare to model-routing.md cost matrix
3. Update if changed > 10%
4. Recalculate monthly cost estimates

---

## Verification Workflow

### Manual Check (Monthly)

```
1. Pick module due for verification
2. Open official documentation
3. Compare specs in prompt to current docs
4. Note any changes
5. Test the changes if significant
6. Update prompt file
7. Update "Last Verified" date
8. Update this schedule
9. Commit changes with message: "chore: verify [module] specs - [date]"
```

### Automated Check (Weekly for critical items)

Use n8n workflow to:
1. Check logo URLs (HTTP HEAD request)
2. Check if Shopify API version is still supported
3. Check if AI model versions have changed
4. Send Slack alert if any checks fail

**n8n Workflow:** See `integrations/n8n-verification-workflow.json`

---

## When Breaking Changes Occur

### High Impact (Social platform character limit change)

1. **Urgent:** Update prompt immediately
2. Test affected workflows
3. Notify content team
4. Backfill any failed generations

### Medium Impact (AI tool parameter syntax change)

1. Update prompt within 1 week
2. Test new syntax
3. Keep old syntax in comments for reference
4. Update docs

### Low Impact (Minor UI change)

1. Note in changelog
2. Update on next scheduled verification
3. No immediate action needed

---

## Verification Log Template

Keep a log of all verifications:

```markdown
## Verification Log

### 2025-12-06 | social/formats/all-platforms.md
- **Checked by:** Jeff
- **Changes found:** Twitter/X Premium now allows 25,000 characters
- **Action taken:** Updated specs, added note
- **Next check:** 2026-01-06

### 2025-12-06 | media/images/prompt-templates.md
- **Checked by:** Jeff
- **Changes found:** Midjourney now on v6.1 (was v6.0)
- **Action taken:** Updated version reference
- **Next check:** 2026-01-06
```

---

## Checklist for New Prompts

When adding a new prompt module:

☐ Add verification schedule entry  
☐ Link to official documentation  
☐ Include version numbers  
☐ Add "Last Verified" date  
☐ Document specs that may change  
☐ Set appropriate check frequency  
☐ Add to automated checks if critical  

---

## Emergency Verification

If content is failing or performing poorly:

1. **Immediate:** Check last verification date
2. Visit official docs
3. Compare specs line by line
4. Test failing case
5. Update and deploy fix
6. Document incident
7. Consider increasing check frequency

---

_Built by Trending Society · Aligned with Core Rule 3_

