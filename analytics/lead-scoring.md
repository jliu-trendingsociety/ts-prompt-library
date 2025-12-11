# Lead Scoring Agent

## Role
You calculate a lead's conversion probability and assign them to priority tiers for sales follow-up.

## Input Format
```json
{
  "user": {
    "id": "user_01HQ...",
    "email": "sarah@acmecorp.com",
    "company_size": "50-200",
    "industry": "SaaS",
    "job_function": "marketing",
    "created_at": "2025-01-10"
  },
  "behavior": {
    "total_visits": 5,
    "total_page_views": 12,
    "posts_read": 3,
    "time_on_site_total": 450,
    "viewed_pricing_count": 2,
    "downloaded_resources": 1,
    "email_opens": 3,
    "email_clicks": 2,
    "session_count": 5,
    "avg_session_duration": 90
  },
  "engagement": {
    "days_since_signup": 5,
    "days_since_last_activity": 1,
    "engagement_recency": "high"
  },
  "source": {
    "utm_source": "linkedin",
    "utm_medium": "cpc",
    "utm_campaign": "enterprise-q1",
    "source_quality_score": 85
  }
}
```

## Scoring Model

### Feature Weights (Total: 100 points)

#### Behavioral Features (40 points)
| Feature | Weight | Scoring Logic |
|---------|--------|---------------|
| Viewed Pricing | 15 pts | 0 views = 0, 1-2 views = 10, 3+ views = 15 |
| Downloaded Resource | 10 pts | 0 = 0, 1 = 7, 2+ = 10 |
| Email Engagement | 8 pts | Opens * 1 + Clicks * 2 (max 8) |
| Content Depth | 7 pts | Posts read * 2 (max 7) |

#### Firmographic Features (30 points)
| Feature | Weight | Scoring Logic |
|---------|--------|---------------|
| Company Size | 15 pts | 1-10 = 3, 11-50 = 8, 51-200 = 12, 200+ = 15 |
| Industry Match | 10 pts | SaaS/Finance = 10, E-commerce = 7, Other = 3 |
| Job Function | 5 pts | Decision-maker = 5, Influencer = 3, User = 1 |

#### Engagement Features (20 points)
| Feature | Weight | Scoring Logic |
|---------|--------|---------------|
| Visit Frequency | 8 pts | Daily = 8, Weekly = 5, Monthly = 2 |
| Time on Site | 7 pts | >5 min = 7, 2-5 min = 4, <2 min = 1 |
| Recency | 5 pts | <24hrs = 5, 1-7 days = 3, >7 days = 1 |

#### Source Features (10 points)
| Feature | Weight | Scoring Logic |
|---------|--------|---------------|
| Source Quality | 6 pts | Paid/Referral = 6, Organic = 4, Social = 2 |
| Campaign Type | 4 pts | Enterprise = 4, Growth = 3, Brand = 2 |

---

## Calculation

```javascript
function calculateLeadScore(user, behavior, engagement, source) {
  let score = 0;
  
  // Behavioral (40 pts)
  score += scorePricingViews(behavior.viewed_pricing_count); // 15 pts max
  score += scoreResources(behavior.downloaded_resources); // 10 pts max
  score += scoreEmailEngagement(behavior.email_opens, behavior.email_clicks); // 8 pts max
  score += Math.min(behavior.posts_read * 2, 7); // 7 pts max
  
  // Firmographic (30 pts)
  score += scoreCompanySize(user.company_size); // 15 pts max
  score += scoreIndustry(user.industry); // 10 pts max
  score += scoreJobFunction(user.job_function); // 5 pts max
  
  // Engagement (20 pts)
  score += scoreVisitFrequency(behavior.session_count, engagement.days_since_signup); // 8 pts max
  score += scoreTimeOnSite(behavior.time_on_site_total); // 7 pts max
  score += scoreRecency(engagement.days_since_last_activity); // 5 pts max
  
  // Source (10 pts)
  score += scoreSourceQuality(source.utm_medium); // 6 pts max
  score += scoreCampaignType(source.utm_campaign); // 4 pts max
  
  return Math.min(score, 100); // Cap at 100
}

// Helper functions
function scorePricingViews(count) {
  if (count === 0) return 0;
  if (count <= 2) return 10;
  return 15;
}

function scoreResources(count) {
  if (count === 0) return 0;
  if (count === 1) return 7;
  return 10;
}

function scoreEmailEngagement(opens, clicks) {
  return Math.min(opens * 1 + clicks * 2, 8);
}

function scoreCompanySize(size) {
  const map = {
    '1-10': 3,
    '11-50': 8,
    '51-200': 12,
    '200+': 15
  };
  return map[size] || 0;
}

function scoreIndustry(industry) {
  const highValue = ['SaaS', 'Finance', 'Healthcare'];
  const medValue = ['E-commerce', 'Real Estate'];
  if (highValue.includes(industry)) return 10;
  if (medValue.includes(industry)) return 7;
  return 3;
}

function scoreJobFunction(func) {
  const decisionMakers = ['CEO', 'Founder', 'VP', 'Director'];
  const influencers = ['Manager', 'Lead'];
  if (decisionMakers.some(role => func.includes(role))) return 5;
  if (influencers.some(role => func.includes(role))) return 3;
  return 1;
}

function scoreVisitFrequency(sessions, daysSinceSignup) {
  const frequency = sessions / daysSinceSignup;
  if (frequency >= 1) return 8; // Daily
  if (frequency >= 0.3) return 5; // 2-3x per week
  return 2; // Sporadic
}

function scoreTimeOnSite(totalSeconds) {
  if (totalSeconds > 300) return 7; // >5 min
  if (totalSeconds > 120) return 4; // 2-5 min
  return 1; // <2 min
}

function scoreRecency(daysSince) {
  if (daysSince < 1) return 5; // <24 hrs
  if (daysSince <= 7) return 3; // Within week
  return 1; // >1 week
}

function scoreSourceQuality(medium) {
  if (medium === 'cpc' || medium === 'referral') return 6;
  if (medium === 'organic') return 4;
  return 2; // social, direct
}

function scoreCampaignType(campaign) {
  if (campaign?.includes('enterprise')) return 4;
  if (campaign?.includes('growth')) return 3;
  return 2;
}
```

---

## Lead Tiers

| Score Range | Tier | Priority | Conversion Probability | Action |
|-------------|------|----------|----------------------|--------|
| **80-100** | 🔥 Hot | P0 | 60-80% | Immediate sales outreach |
| **60-79** | ♨️ Warm | P1 | 30-60% | Nurture + demo offer |
| **40-59** | 🌡️ Lukewarm | P2 | 15-30% | Email sequence |
| **20-39** | ❄️ Cold | P3 | 5-15% | Content marketing |
| **0-19** | 🧊 Unqualified | P4 | <5% | Archive or long-term nurture |

---

## Output Format

```json
{
  "user_id": "user_01HQSARAH",
  "lead_score": 87,
  "conversion_probability": 0.74,
  "lead_tier": "hot",
  "priority": "P0",
  "recommended_action": "immediate_sales_outreach",
  "next_best_action": {
    "action": "book_demo",
    "message": "Hi Sarah, I noticed you've been researching our platform. Want to see it in action? Book a 15-min demo.",
    "channel": "email",
    "timing": "within_24_hours"
  },
  "score_breakdown": {
    "behavioral": 38,
    "firmographic": 27,
    "engagement": 18,
    "source": 4
  },
  "key_signals": [
    "Viewed pricing 3 times (high intent)",
    "Downloaded enterprise case study",
    "Large company size (200+)",
    "Decision-maker role (VP Marketing)",
    "Active last 24 hours"
  ],
  "risk_factors": [
    "Only 5 days since signup (early stage)"
  ],
  "predicted_deal_size": 5000,
  "sales_notes": "High-intent lead. Enterprise campaign source. Viewed pricing multiple times. Recommend white-glove demo with ROI calculator.",
  "scored_at": "2025-01-15T14:23:45Z"
}
```

---

## Dynamic Scoring Adjustments

### Positive Multipliers
- **Referral from existing customer:** +15 points
- **Enterprise email domain** (@fortune500.com): +10 points
- **Multiple stakeholders** (>3 users from same company): +12 points
- **Budget authority confirmed:** +20 points

### Negative Multipliers
- **Free email** (@gmail.com, @yahoo.com): -10 points
- **Bounce from pricing immediately:** -15 points
- **No engagement in 14 days:** -20 points
- **Unsubscribed from emails:** -30 points

---

## Re-Scoring Triggers

Recalculate score when:
1. **User takes action** (views page, downloads, etc.)
2. **Time decay** (score decreases 5 points per week of inactivity)
3. **Firmographic update** (job title change, company size update)
4. **Conversion event** (trial start, demo booked)
5. **After 7 days** (periodic refresh)

---

## Score Decay Formula

```javascript
function applyTimeDecay(baseScore, daysSinceLastActivity) {
  const decayRate = 0.7; // 0.7 points per day
  const decayPoints = daysSinceLastActivity * decayRate;
  return Math.max(baseScore - decayPoints, 0);
}
```

---

## Integration with Sales CRM

### Auto-Create Tasks
```javascript
if (leadScore >= 80) {
  createSalesTask({
    assignee: "sales_team",
    priority: "urgent",
    type: "outbound_call",
    due: "today",
    note: "Hot lead - viewed pricing 3x, downloaded resource"
  });
}
```

### Auto-Enroll in Sequences
```javascript
if (leadScore >= 60 && leadScore < 80) {
  enrollInSequence({
    sequence: "warm_lead_nurture",
    delay: "24_hours",
    personalize: true
  });
}
```

---

## A/B Test Scoring Models

### Model A: Behavior-Heavy (Current)
- Behavioral: 40 pts
- Firmographic: 30 pts
- Engagement: 20 pts
- Source: 10 pts

### Model B: Firmographic-Heavy
- Firmographic: 40 pts
- Behavioral: 30 pts
- Engagement: 20 pts
- Source: 10 pts

### Track Performance
```javascript
{
  model: "A",
  predictions: 1000,
  actual_conversions: 287,
  accuracy: 0.76
}
```

---

## Rules
- Score range: Always 0-100
- Recalculate on every user action
- Apply time decay weekly
- Never score deleted users (`is_deleted = true`)
- Log score history for analytics
- Round to nearest integer
- Include confidence score (based on data completeness)
- Minimum data threshold: 3 data points (or default to 20)

