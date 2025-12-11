# Trending Society Image Prompt Templates Module

> **Version:** 1.0  
> **Updated:** December 2025  
> **Last Verified:** December 6, 2025  
> **Purpose:** Generate platform-optimized image prompts for AI image tools  
> **Tools:** Midjourney, DALL-E, Flux, Ideogram, Stable Diffusion  
> **Rule Alignment:** Core Rule 3 - Documentation First

---

## AI Tool Versions & Documentation

**⚠️ IMPORTANT:** Verify tool versions monthly. Parameters change with new releases.

| Tool               | Current Version | Docs Link                                           | Last Verified | Notes                                      |
| ------------------ | --------------- | --------------------------------------------------- | ------------- | ------------------------------------------ |
| Midjourney         | v6.1            | [Docs](https://docs.midjourney.com/)                | Dec 2025      | v7 in alpha, may change parameters         |
| DALL-E             | DALL-E 3        | [Docs](https://platform.openai.com/docs/guides/images) | Dec 2025   | Quality improvements over DALL-E 2         |
| Flux               | Flux Pro 1.1    | [Docs](https://docs.flux.ai/)                       | Dec 2025      | Flux Pro recommended over Schnell          |
| Ideogram           | v2.0            | [Docs](https://ideogram.ai/docs)                    | Dec 2025      | Best for text-in-image generation          |
| Stable Diffusion   | SDXL 1.0        | [Docs](https://stability.ai/stable-diffusion)       | Dec 2025      | Open source, requires local/cloud setup    |

---

## Platform Image Specifications

| Platform              | Aspect Ratio  | Dimensions             | Best Use                        |
| --------------------- | ------------- | ---------------------- | ------------------------------- |
| Instagram Feed        | 1:1 or 4:5    | 1080x1080 or 1080x1350 | Posts, carousels                |
| Instagram Story/Reels | 9:16          | 1080x1920              | Stories, vertical video covers  |
| LinkedIn              | 1.91:1 or 1:1 | 1200x627 or 1080x1080  | Posts, articles                 |
| Twitter/X             | 16:9 or 1:1   | 1600x900 or 1080x1080  | Posts, headers                  |
| Facebook              | 1.91:1        | 1200x630               | Posts, link previews            |
| YouTube Thumbnail     | 16:9          | 1280x720               | Video thumbnails                |
| Blog/OG Image         | 1.91:1        | 1200x630               | Social sharing, featured images |

---

## Trending Society Visual Brand Guidelines

**Color palette:**

- Primary: Deep navy (#1a1a2e), Warm white (#fafafa)
- Accent: Electric blue (#4361ee), Soft coral (#ff6b6b)
- Neutral: Slate gray (#64748b)

**Visual style:**

- Clean, minimal, modern
- Abstract tech/nature fusion when possible
- Avoid generic stock photo aesthetics
- Favor geometric shapes, light gradients, subtle texture
- Human elements should feel candid, not posed

**Typography in images:**

- Sans-serif, bold for headlines
- High contrast for readability
- Minimal text on images (let the visual do the work)

---

## Prompt Architecture

Every image prompt should follow this structure:

```
[SUBJECT] + [STYLE] + [COMPOSITION] + [LIGHTING] + [MOOD] + [TECHNICAL SPECS]
```

**Components:**

| Component   | Purpose              | Examples                                                          |
| ----------- | -------------------- | ----------------------------------------------------------------- |
| Subject     | What's in the image  | "a founder working at a standing desk," "abstract neural network" |
| Style       | Visual treatment     | "minimal illustration," "3D render," "editorial photography"      |
| Composition | How it's framed      | "centered," "rule of thirds," "negative space on left"            |
| Lighting    | Light quality        | "soft natural light," "dramatic side lighting," "golden hour"     |
| Mood        | Emotional tone       | "calm and focused," "energetic," "contemplative"                  |
| Technical   | Tool-specific params | "--ar 16:9 --v 6" (Midjourney), "photorealistic, 8k" (DALL-E)     |

---

## Prompt Templates by Category

### 1. Founder/Creator Portraits

**Use for:** LinkedIn posts, about pages, personal brand content

```
Midjourney:
A [gender] founder in their [30s/40s], [ethnicity] with [hair description], wearing [minimal smart casual outfit], working at a [modern desk/standing desk] in a [bright loft office/minimalist home office], soft natural window light, shallow depth of field, editorial photography style, calm and focused mood --ar 4:5 --v 6 --style raw

DALL-E:
Editorial photograph of a confident entrepreneur, [age range], sitting in a modern minimalist office with large windows, natural lighting, wearing a simple [color] sweater, looking thoughtfully at camera, shallow depth of field, professional but approachable, high resolution
```

---

### 2. Abstract Tech/AI Concepts

**Use for:** Blog headers, thought leadership posts, product announcements

```
Midjourney:
Abstract visualization of [concept: neural networks/data flow/automation], glowing [blue/coral] lines forming geometric patterns on dark navy background, minimal and elegant, soft gradient lighting, futuristic but warm, no text --ar 1.91:1 --v 6

DALL-E:
Abstract 3D render representing artificial intelligence, flowing geometric shapes in electric blue and soft coral gradients, dark background, clean minimal aesthetic, subtle glow effects, modern and sophisticated
```

**Concept variations:**

- Neural networks: "interconnected nodes with flowing data streams"
- Automation: "mechanical and organic forms merging seamlessly"
- AI agents: "geometric humanoid silhouette made of light particles"
- Workflows: "flowing lines connecting minimal icons"

---

### 3. Product/SaaS Mockups

**Use for:** Product launches, feature announcements, landing pages

```
Midjourney:
Clean product mockup of a [laptop/phone/tablet] displaying a [dashboard/app interface], floating on minimal gradient background, soft shadows, professional product photography style, [electric blue] accent lighting --ar 16:9 --v 6

DALL-E:
Minimalist product photograph of a MacBook Pro displaying a clean SaaS dashboard interface, floating against a soft gradient background from navy to white, subtle reflection beneath, professional studio lighting
```

---

### 4. Workspace/Environment Shots

**Use for:** Culture content, hiring posts, behind-the-scenes

```
Midjourney:
Modern creative workspace, standing desk with minimal setup, large monitor, small plant, natural light from large windows, clean and organized, warm but professional atmosphere, editorial interior photography --ar 16:9 --v 6

DALL-E:
Photograph of a minimalist home office setup, wooden standing desk, single monitor with clean interface, succulent plant, morning light streaming through window, calm productive atmosphere, high resolution interior photography
```

---

### 5. Conceptual/Editorial Illustrations

**Use for:** Blog posts, newsletters, social content about ideas

```
Midjourney:
Editorial illustration of [concept], minimal line art style, [navy and coral] color palette, clean white background, sophisticated and modern, suitable for business publication --ar 1:1 --v 6

DALL-E:
Minimalist illustration representing [concept], simple geometric shapes, limited color palette of deep blue and coral on white, clean lines, modern editorial style, suitable for professional publication
```

**Concept examples:**

- "the intersection of AI and human creativity"
- "scaling a business through systems"
- "building community around a product"
- "the creator economy flywheel"

---

### 6. Data Visualization Aesthetics

**Use for:** Reports, case studies, statistics posts

```
Midjourney:
Abstract data visualization, flowing bar charts and line graphs morphing into organic shapes, [blue gradient] on dark background, minimal and elegant, infographic aesthetic but artistic --ar 1.91:1 --v 6

DALL-E:
Artistic interpretation of data visualization, abstract charts and graphs with glowing elements, dark navy background with electric blue accents, clean modern aesthetic, professional but creative
```

---

### 7. YouTube Thumbnails

**Use for:** Video content, podcast clips, educational content

```
Midjourney:
YouTube thumbnail composition, [subject/person] on left third, bold negative space on right for text, dramatic but clean lighting, high contrast, energetic mood, professional but approachable --ar 16:9 --v 6

DALL-E:
Professional YouTube thumbnail layout, clear subject on left side with space for text overlay on right, bright and high contrast, engaging expression/scene, clean background that won't compete with text
```

**Thumbnail rules:**

- Face visible and expressive (if using person)
- High contrast for small-screen visibility
- Leave clear space for text overlay
- Avoid clutter, one focal point

---

### 8. Social Proof/Testimonial Backgrounds

**Use for:** Customer quotes, case study highlights, reviews

```
Midjourney:
Minimal abstract background for text overlay, soft gradient from [navy to slate], subtle geometric texture, professional and clean, no distracting elements --ar 1:1 --v 6

DALL-E:
Simple elegant background suitable for text overlay, soft gradient in deep blue tones, subtle texture, minimal and professional, high resolution
```

---

## Tool-Specific Parameters

### Midjourney

```
Aspect ratios: --ar 1:1, --ar 4:5, --ar 16:9, --ar 9:16, --ar 1.91:1
Version: --v 6 (current), --v 5.2 (stable)
Style: --style raw (photorealistic), --stylize [0-1000] (creativity level)
Quality: --q 2 (higher quality, slower)
No: --no [element] (exclude something)
```

### DALL-E

```
Add to prompt end: "high resolution, professional quality, 8k"
Be explicit about style: "photograph," "illustration," "3D render"
Specify what to avoid: "no text, no watermarks, no busy backgrounds"
```

### Flux/Stable Diffusion

```
Add quality tags: "masterpiece, best quality, highly detailed"
Negative prompt: "blurry, low quality, distorted, text, watermark"
Specify dimensions in generation settings, not prompt
```

---

## Output Format

When generating image prompts, output in this structure:

```
### Image Concept: [Brief description]

**Platform:** [Where this will be used]
**Aspect ratio:** [X:Y]
**Dimensions:** [WxH pixels]

**Midjourney prompt:**
[Full prompt with parameters]

**DALL-E prompt:**
[Full prompt optimized for DALL-E]

**Alt text for SEO:**
[Descriptive alt text including primary keyword]

**Visual notes:**
[Any additional guidance for the image]
```

---

## Quality Gate

Before outputting image prompts, verify:

☐ Aspect ratio matches intended platform
☐ Style aligns with Trending Society brand guidelines
☐ Prompt includes all six components (subject, style, composition, lighting, mood, technical)
☐ No generic stock photo language ("business team high-fiving")
☐ Alt text is descriptive and includes relevant keyword
☐ Prompt is specific enough to generate consistent results
☐ Tool-specific parameters are included

---

## Anti-Patterns (Avoid These)

- "A professional business meeting" (too generic)
- "Happy diverse team collaborating" (stock photo cliche)
- "Futuristic technology background" (meaningless)
- "Clean modern design" (too vague)
- Overly complex prompts with 15+ elements (confuses the model)
- Requesting text in images (AI handles this poorly)

---

_Module built by Trending Society_
