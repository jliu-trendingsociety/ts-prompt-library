# Author Registry

> **Version:** 1.0  
> **Updated:** December 12, 2025  
> **Purpose:** Centralized author profiles for E-E-A-T signals and consistent attribution

---

## Overview

This registry contains author profiles for use in JSON-LD schema markup. Each author includes:

- Personal identifiers and role
- Social/professional profile links (sameAs)
- Areas of expertise (verticals)
- Bio for author pages

---

## Author Profiles

### Jeff Liu

| Field            | Value                                                     |
| ---------------- | --------------------------------------------------------- |
| **Name**         | Jeff Liu                                                  |
| **Title**        | Founder & CEO                                             |
| **Organization** | Trending Society                                          |
| **URL**          | `https://trendingsociety.com/team/jeff-liu`               |
| **Photo**        | `https://trendingsociety.com/images/authors/jeff-liu.jpg` |
| **Expertise**    | Tech, AI, Business, Automation                            |

**sameAs Links:**

```json
["https://linkedin.com/in/jeffliu", "https://twitter.com/jeffliu"]
```

**Bio:**

> Jeff Liu is the Founder and CEO of Trending Society, an AI-native digital agency specializing in automation, content systems, and social commerce. He writes about emerging technology, AI tools, and the future of digital work.

---

## JSON-LD Template

Use this template when generating author schema in articles:

```json
{
  "author": {
    "@type": "Person",
    "name": "[Author Name]",
    "url": "[Author Profile URL]",
    "image": "[Author Photo URL]",
    "jobTitle": "[Job Title]",
    "worksFor": {
      "@type": "Organization",
      "name": "Trending Society",
      "url": "https://trendingsociety.com"
    },
    "sameAs": ["[LinkedIn URL]", "[Twitter URL]"],
    "description": "[Short bio]"
  }
}
```

---

## Usage Instructions

1. **Identify the author** based on content vertical or default to primary author
2. **Include author schema** in the JSON-LD output (Section 8 of master prompt)
3. **Use sameAs links** for entity authority and E-E-A-T signals
4. **Match expertise** to article vertical when possible

---

## Adding New Authors

When adding a new author, include:

1. Full name
2. Job title
3. Profile URL on trendingsociety.com
4. Photo URL
5. At least 2 sameAs links (LinkedIn required)
6. Areas of expertise (verticals)
7. Short bio (2-3 sentences)

---

## Why Author Attribution Matters

| Benefit             | Impact                                             |
| ------------------- | -------------------------------------------------- |
| **E-E-A-T Signals** | Google's ranking factor for expertise and trust    |
| **AI Citation**     | LLMs cite sources with clear authorship more often |
| **Knowledge Graph** | Builds author entity in Google's knowledge graph   |
| **Brand Authority** | Associates quality content with named experts      |

---

_Registry maintained by Trending Society_
