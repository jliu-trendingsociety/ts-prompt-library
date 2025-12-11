# User Context Template

## Purpose
This standardized context format is passed to all AI agents to ensure consistent personalization across the orchestration system.

## Full Context Schema

```json
{
  "user": {
    "id": "user_01HQVK3N2MABCDEF",
    "canonical_id": "recABC123",
    "tenant_id": "org_01HQTRENDING",
    "brand_id": "brand_01HQMAIN",
    
    "profile": {
      "email": "sarah@acmecorp.com",
      "name": "Sarah Johnson",
      "first_name": "Sarah",
      "last_name": "Johnson",
      "company_name": "ACME Corp",
      "job_function": "Marketing Manager",
      "industry": "SaaS",
      "company_size": "50-200",
      "revenue_range": "$5-10M"
    },
    
    "scores": {
      "engagement_score": 75,
      "lead_score": 87,
      "wtp_score": 82,
      "conversion_probability": 0.74,
      "churn_risk": 0.12
    },
    
    "segmentation": {
      "segment_id": "seg_frustrated_marketers",
      "segment_name": "Frustrated Marketers - High Intent",
      "confidence": 0.87,
      "primary_pain_point": "time_management",
      "preferred_content_types": ["how-to", "case-study"]
    },
    
    "lifecycle": {
      "stage": "trial",
      "days_since_signup": 5,
      "days_until_trial_end": 9,
      "created_at": "2025-01-10T14:23:45Z",
      "last_login_at": "2025-01-15T10:15:00Z"
    }
  },
  
  "session": {
    "session_id": "sess_01HQVK3N2MSESSION",
    "device_type": "desktop",
    "browser": "Chrome",
    "geo_country": "US",
    "geo_city": "San Francisco",
    
    "current_visit": {
      "duration_seconds": 180,
      "pages_viewed": 5,
      "scroll_depth_avg": 75,
      "current_page": "/blog/ai-automation",
      "referrer_url": "https://google.com/search?q=ai+social+automation"
    },
    
    "utm_params": {
      "utm_source": "linkedin",
      "utm_medium": "cpc",
      "utm_campaign": "enterprise-q1-2025",
      "utm_content": "carousel-variant-a",
      "utm_term": "ai+automation"
    }
  },
  
  "behavior": {
    "total_visits": 5,
    "total_page_views": 12,
    "total_time_on_site": 450,
    "session_count": 5,
    "avg_session_duration": 90,
    
    "content_engagement": {
      "posts_viewed": ["post_01HQABC", "post_01HQXYZ", "post_01HQDEF"],
      "posts_read": 3,
      "categories_viewed": ["ai-automation", "social-media", "content-marketing"],
      "favorite_categories": ["ai-automation"],
      "time_by_category": {
        "ai-automation": 300,
        "social-media": 150
      }
    },
    
    "intent_signals": {
      "viewed_pricing": true,
      "viewed_pricing_count": 3,
      "downloaded_resources": ["case-study-saas", "roi-calculator"],
      "watched_demo": false,
      "submitted_form": true,
      "abandoned_checkout": false
    },
    
    "communication": {
      "email_opens": 3,
      "email_clicks": 2,
      "last_email_sent": "2025-01-14T09:00:00Z",
      "subscribed": true,
      "preferred_channel": "email"
    }
  },
  
  "history": {
    "first_touch": {
      "source": "linkedin",
      "medium": "cpc",
      "campaign": "enterprise-q1-2025",
      "landing_page": "/",
      "timestamp": "2025-01-10T14:23:45Z"
    },
    
    "key_events": [
      {
        "event_type": "signup",
        "timestamp": "2025-01-10T14:30:00Z"
      },
      {
        "event_type": "first_post_view",
        "post_id": "post_01HQABC",
        "timestamp": "2025-01-11T09:15:00Z"
      },
      {
        "event_type": "viewed_pricing",
        "timestamp": "2025-01-12T11:30:00Z"
      },
      {
        "event_type": "downloaded_resource",
        "resource_id": "case-study-saas",
        "timestamp": "2025-01-13T15:45:00Z"
      }
    ],
    
    "purchase_history": [],
    "support_tickets": [],
    "feature_usage": {
      "blog_generator": 0,
      "social_scheduler": 0,
      "ai_assistant": 2
    }
  },
  
  "context": {
    "request_type": "personalize",
    "page_context": "homepage",
    "timestamp": "2025-01-15T14:23:45Z",
    "timezone": "America/Los_Angeles"
  }
}
```

---

## Minimal Context (For Fast Operations)

When speed is critical, use this minimal schema:

```json
{
  "user_id": "user_01HQVK3N2MABCDEF",
  "segment_id": "seg_frustrated_marketers",
  "wtp_score": 82,
  "lead_score": 87,
  "session_id": "sess_01HQVK...",
  "device_type": "desktop",
  "intent_signals": {
    "viewed_pricing": true,
    "downloaded_resource": true
  }
}
```

---

## Context Enrichment

### How to Build Full Context from User ID

```javascript
async function buildUserContext(userId) {
  // 1. Get user profile
  const user = await airtable.get('users', userId);
  
  // 2. Get current session
  const session = await getCurrentSession(userId);
  
  // 3. Get behavior data (aggregated)
  const behavior = await getBehaviorStats(userId);
  
  // 4. Get recent events
  const history = await getRecentEvents(userId, {limit: 10});
  
  // 5. Calculate scores (if not cached)
  const scores = await getOrCalculateScores(userId);
  
  // 6. Get segment
  const segment = await getUserSegment(userId);
  
  // 7. Combine
  return {
    user: {
      id: userId,
      profile: user,
      scores: scores,
      segmentation: segment,
      lifecycle: {
        stage: user.lifecycle_stage,
        days_since_signup: calculateDays(user.created_at)
      }
    },
    session: session,
    behavior: behavior,
    history: history,
    context: {
      request_type: null, // Set by caller
      timestamp: new Date().toISOString()
    }
  };
}
```

---

## Context Caching

### Cache Strategy
```javascript
// Cache full context for 5 minutes
const cacheKey = `user_context:${userId}`;
const cached = await redis.get(cacheKey);

if (cached && Date.now() - cached.timestamp < 300000) {
  return JSON.parse(cached.data);
}

const fresh = await buildUserContext(userId);
await redis.set(cacheKey, JSON.stringify({
  data: fresh,
  timestamp: Date.now()
}), 'EX', 300); // 5 min TTL

return fresh;
```

---

## Context Validation

### Required Fields
Every context MUST have:
- `user.id`
- `user.segment_id`
- `user.scores.wtp_score`
- `context.request_type`
- `context.timestamp`

### Validation Function
```javascript
function validateContext(context) {
  const required = [
    'user.id',
    'user.segmentation.segment_id',
    'user.scores.wtp_score',
    'context.request_type',
    'context.timestamp'
  ];
  
  for (const path of required) {
    if (!getNestedValue(context, path)) {
      throw new Error(`Missing required field: ${path}`);
    }
  }
  
  return true;
}
```

---

## Privacy & Compliance

### PII Handling
```javascript
// For logging or external APIs, redact PII
function redactPII(context) {
  return {
    ...context,
    user: {
      ...context.user,
      profile: {
        ...context.user.profile,
        email: hashEmail(context.user.profile.email),
        name: null,
        first_name: null,
        last_name: null
      }
    }
  };
}
```

### GDPR Compliance
- Store consent status in `user.privacy.gdpr_consent`
- Respect opt-outs in `user.privacy.marketing_opt_out`
- Delete user data on request (set `is_deleted = true`)

---

## Usage Examples

### Example 1: Personalize Homepage
```javascript
const context = await buildUserContext(userId);
context.context.request_type = 'personalize';
context.context.page_context = 'homepage';

const result = await orchestrator.call({
  type: 'personalize',
  context: context
});
```

### Example 2: Generate Ad Creative
```javascript
const context = await buildUserContext(userId);
context.context.request_type = 'creative';
context.context.platform = 'facebook';

const result = await orchestrator.call({
  type: 'creative',
  context: context
});
```

### Example 3: Score Lead
```javascript
const context = await buildUserContext(userId);
context.context.request_type = 'score';

const result = await orchestrator.call({
  type: 'score',
  context: context
});
```

---

## Testing

### Sample Test Context
```json
{
  "user": {
    "id": "user_test_01",
    "profile": {
      "name": "Test User",
      "email": "test@example.com",
      "company_name": "Test Corp",
      "company_size": "50-200"
    },
    "scores": {
      "wtp_score": 75,
      "lead_score": 80
    },
    "segmentation": {
      "segment_id": "seg_general"
    }
  },
  "context": {
    "request_type": "test",
    "timestamp": "2025-01-15T14:23:45Z"
  }
}
```

---

## Rules
- Always validate context before passing to agents
- Cache context for 5 minutes (same user + session)
- Redact PII when logging
- Include timezone for time-based personalization
- Update context incrementally (don't rebuild on every request)
- Store context building errors in `orchestration_log`

