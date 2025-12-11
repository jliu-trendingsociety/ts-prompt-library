# Quick Start Guide - Trending Society Prompts v2.1

> **For:** Content teams, developers, automation engineers  
> **Time to deploy:** 30 minutes  
> **Last updated:** December 6, 2025

---

## 🚀 Fastest Path to First Content

### Step 1: Choose Your Content Type (2 minutes)

```bash
# Routine news article (cheap, fast)
→ Use: editorial/v5/master-prompt-lite.md
→ Model: GPT-4o-mini
→ Cost: $0.001-0.003
→ Time: ~30 seconds

# Complex analysis or brand-critical (premium)
→ Use: editorial/v5/master-prompt.md
→ Model: Claude Sonnet 4
→ Cost: $0.015-0.025
→ Time: ~60 seconds

# Social media post
→ Use: social/formats/all-platforms.md
→ Model: Gemini Flash 2.0
→ Cost: $0.0004
→ Time: ~10 seconds
```

### Step 2: Set Up n8n Workflow (20 minutes)

**Option A: Import Pre-Built**
1. Open `integrations/n8n-blog-post-example.md`
2. Copy workflow JSON
3. Import into n8n
4. Configure credentials (5 mins)
5. Test with sample article (2 mins)

**Option B: Use Webhook Directly**
```bash
curl -X POST https://your-n8n.com/webhook/generate-blog \
  -H "Content-Type: application/json" \
  -d '{
    "source_url": "https://techcrunch.com/article",
    "content_type": "blog-post",
    "canonical_id": "article_009",
    "prompt_version": "v5-lite"
  }'
```

### Step 3: Configure Logging (5 minutes)

**Airtable Setup:**
1. Create table: `content_logs`
2. Add fields from `core/logging-schema.md`
3. Connect to n8n workflow
4. Done!

---

## 📋 Decision Trees

### "Which prompt should I use?"

```
Is it brand-critical content? (founder blog, press release)
└─ YES → editorial/v5/master-prompt.md (Claude Sonnet)
└─ NO → Is it complex analysis or synthesis?
    └─ YES → editorial/v5/master-prompt.md (Claude Sonnet)
    └─ NO → editorial/v5/master-prompt-lite.md (GPT-4o-mini)
```

### "Which model should I use?"

```
See: core/model-routing.md

Quick rules:
- Simple task (social post, ad copy) → Gemini Flash
- Moderate task (email, summary) → Gemini Pro or GPT-4o-mini
- Complex task (blog post, analysis) → Claude Sonnet or GPT-4o
- Critical task (founder voice, legal) → Claude Opus
```

---

## 🔧 Essential Configuration

### Environment Variables

```bash
# AI Models
OPENAI_API_KEY=sk-...
ANTHROPIC_API_KEY=sk-ant-...
GOOGLE_AI_API_KEY=...

# Shopify
SHOPIFY_SHOP_NAME=trending-society
SHOPIFY_API_KEY=shpat_...
SHOPIFY_BLOG_ID=123456789
DEFAULT_AUTHOR=Jeffrey Liu

# Airtable
AIRTABLE_API_KEY=key...
AIRTABLE_BASE_ID=app...

# Slack
SLACK_WEBHOOK_URL=https://hooks.slack.com/...

# Budget
DAILY_BUDGET_USD=50.00
MONTHLY_BUDGET_USD=300.00
```

### Required Tables (Airtable)

**Table: content_logs**
```
Fields:
- timestamp (datetime)
- canonical_id (text)
- content_type (select)
- model_used (select)
- tokens_total (number)
- cost_usd (number)
- result (select: success/failure)
- quality_score (number)
```

**Table: content_index**
```
Fields:
- canonical_id (text, unique)
- title (text)
- current_url (text)
- previous_urls (array)
- shopify_article_id (number)
- created_at (datetime)
```

---

## 📊 Monitoring

### Daily Checks (5 minutes)

```bash
# Check yesterday's logs
SELECT 
  COUNT(*) as total,
  SUM(cost_usd) as total_cost,
  AVG(quality_score) as avg_quality
FROM content_logs
WHERE date = YESTERDAY;

# Expected:
- Total: 5-20 generations
- Total cost: $0.05-0.50
- Avg quality: 0.80+
```

### Weekly Tasks (15 minutes)

1. Verify logo URLs: Run `integrations/verify-logos.js`
2. Review cost trends: Check Airtable dashboard
3. Update article index: Add new canonical IDs

### Monthly Tasks (30 minutes)

1. Verify platform specs: See `core/verification-schedule.md`
2. Update AI model pricing: Check OpenAI/Anthropic pricing pages
3. Review quality scores: Identify improvement areas

---

## 🆘 Troubleshooting

### "Content generation failed"

**Check:**
1. Is source URL accessible? (test in browser)
2. Is AI model API key valid? (test API call)
3. Are Shopify credentials correct? (test create draft)
4. Check n8n execution log for specific error

**Common fixes:**
- 401 Unauthorized → Update API key
- Timeout → Increase timeout setting (10s → 30s)
- Rate limit → Add delay between requests
- Validation failed → Check quality gate in logs

### "Cost higher than expected"

**Check:**
1. Which model was used? (should be GPT-4o-mini for routine)
2. Token count? (should be <5,000 for blog post)
3. How many retries? (check execution logs)

**Fix:**
- Wrong model → Update `core/model-routing.md` routing
- High token count → Use lite prompt version
- Too many retries → Fix source issue first

### "Content quality too low"

**Check:**
1. Quality score in logs? (target: 0.80+ lite, 0.90+ premium)
2. Which quality gates failed? (see logs)
3. Source content quality? (garbage in, garbage out)

**Fix:**
- Low score → Use premium prompt instead of lite
- Gates failed → Update prompt with specific fixes
- Bad source → Filter or improve source selection

---

## 💡 Pro Tips

### Cost Optimization

1. **Use lite prompts for routine news** (90% savings)
2. **Batch content generation** (process 10+ at once)
3. **Cache extracted content** (don't re-fetch)
4. **Monitor daily spend** (set alerts at $5/day)

### Quality Optimization

1. **Always cite sources** (use badge pattern)
2. **Include 2-5 internal links** (SEO boost)
3. **Validate before publishing** (run quality gates)
4. **A/B test prompts** (lite vs premium)

### Speed Optimization

1. **Use Gemini Flash for simple tasks** (2x faster)
2. **Parallel processing** (don't wait for one to finish)
3. **Pre-process source content** (HTML→Markdown offline)
4. **Cache frequently used data** (article index, source registry)

---

## 📚 Key Documents to Bookmark

**Must Read:**
1. `core/INSTRUCTIONS.md` - Start here
2. `core/model-routing.md` - Model selection
3. `FIXES_IMPLEMENTED.md` - What's new in v2.1

**Implementation:**
4. `integrations/n8n-blog-post-example.md` - Deploy this first
5. `core/logging-schema.md` - Set up tracking

**Reference:**
6. `shared/article-index.md` - Internal links
7. `shared/source-registry.md` - External sources
8. `core/verification-schedule.md` - Maintenance calendar

---

## 🎯 Success Checklist

Before going live:

### Setup Phase
☐ n8n workflow imported and tested  
☐ All API credentials configured  
☐ Airtable tables created  
☐ Environment variables set  
☐ Test article generated successfully  

### Validation Phase
☐ Quality gates passing (0.80+ score)  
☐ Cost per article < $0.005 (lite) or < $0.025 (premium)  
☐ Generation time < 60 seconds  
☐ Logging working (check Airtable)  
☐ Error handling tested (trigger failure)  

### Production Phase
☐ Daily monitoring set up  
☐ Budget alerts configured  
☐ Slack notifications working  
☐ Team trained on prompts  
☐ Documentation reviewed  

---

## 🚨 Emergency Contacts

**If something breaks:**

1. **Check n8n execution logs** (most issues are here)
2. **Review Airtable logs** (errors are logged)
3. **Test API credentials** (often the culprit)
4. **Check circuit breakers** (external resources down?)

**Can't fix it?**
- Post in #engineering Slack channel
- Include: execution ID, error message, what you tried
- Attach: n8n execution log screenshot

---

## 📈 What to Measure

**Week 1:** Focus on reliability
- Generations: 5-10/day
- Success rate: 90%+
- Cost/article: Track baseline

**Week 2:** Optimize costs
- Test lite vs premium prompts
- Measure quality differences
- Find optimal model per use case

**Week 3:** Scale up
- Increase to 20-30 generations/day
- Monitor quality consistency
- Validate cost projections

**Week 4:** Automate
- Set up scheduled workflows
- Enable auto-publishing
- Configure alerts and dashboards

---

## 🎓 Learning Path

**Day 1: Basics**
- Read `core/INSTRUCTIONS.md`
- Run test generation manually
- Review output quality

**Day 2: Implementation**
- Set up n8n workflow
- Configure logging
- Test end-to-end

**Day 3: Optimization**
- Compare lite vs premium
- Test different models
- Measure costs

**Day 4: Production**
- Enable auto-publishing
- Set up monitoring
- Train team

---

**Questions? Check:**
- `README.md` - Overview
- `FIXES_IMPLEMENTED.md` - Detailed changes
- `core/` - Core system guides
- `integrations/` - Implementation examples

_Quick Start Guide v2.1 · Trending Society_

