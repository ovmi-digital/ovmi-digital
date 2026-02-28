# OVMI.DIGITAL Landing Page Design

## Overview

Single-page portfolio-first landing site for OVMI.DIGITAL, a small European software studio. The site's primary goal is showcasing case studies; contact is secondary.

**Stack:** Astro + Tailwind CSS v4 + Bun
**Domain:** ovmi.digital

## Visual Direction

**Style:** Swiss/Brutalist — "The Wall" approach. Full-bleed typographic sections stacked vertically with aggressive whitespace. No navigation bar, pure scroll experience. Near-zero motion.

**Typeface:** Satoshi (Indian Type Foundry, Fontshare) — Variable, self-hosted WOFF2.
- Hero headline: `clamp(3rem, 8vw, 8rem)`, Black (900)
- Section headings: `clamp(2rem, 4vw, 3.5rem)`, Bold (700)
- Labels: `1.125rem`, Bold (700), uppercase, tracked-out
- Body: `1.125rem`, Regular (400), line-height 1.6
- Small/footer: `0.875rem`, Regular (400)

**Colors:**

| Token        | Light          | Dark           |
|--------------|----------------|----------------|
| Background   | `#FAFAFA`      | `#0A0A0A`      |
| Text         | `#000000`      | `#FAFAFA`      |
| Accent       | `#E63946`      | `#F87171`      |
| Muted        | `#6B7280`      | `#9CA3AF`      |

No accent gradients or secondary colors. Accent used only for links, CTAs, and the email address.

**Spacing:**
- Section padding: `clamp(6rem, 12vh, 10rem)` vertical
- Content max-width: `72rem` (1152px), centered
- Images: full-bleed (break out of content container)

## Page Sections

### 1. Hero (100vh)

- Logo mark + "OVMI.DIGITAL" wordmark at top-left, regular weight
- "We make things exist." — Black (900), `clamp(3rem, 8vw, 8rem)`, left-aligned
- "We're builders first. Your project is in good hands." — Regular (400), below headline
- "View our work ↓" — accent red, bottom-left
- Dark mode toggle — small icon, top-right

### 2. About

- Section label: "ABOUT" — uppercase, bold, small, tracked-out
- Prose: max-width ~640px, regular weight, generous line-height
- "Small by choice. Serious by default." — standalone line, bold weight, visual anchor

### 3. What We Do

- Section label: "WHAT WE DO"
- Intro paragraph from copy
- 2-column grid (1-col mobile), 5 service cards:
  - Product development
  - AI integration & automation
  - AI consultation
  - Design
  - Blockchain development
- Cards: bold title + regular description. No borders, no backgrounds. Pure typography.

### 4. How We Work

- Section label: "HOW WE WORK"
- 5 numbered steps, vertical list:
  1. Conversation
  2. Research
  3. Alignment
  4. Build
  5. Launch
- Numbers: Black (900), ~2rem, accent red
- Step titles: Bold (700)
- Descriptions: Regular (400)

### 5. Case Studies (star section)

- Section label: "OUR WORK"
- Per case study (currently Lola.pet):
  - Full-bleed abstract image (Replicate-generated), ~50-60vh
  - Project name: large, bold
  - Tagline: accent red
  - Structured sections: THE PROJECT / WHAT WE BUILT / WHERE IT'S GOING
  - External link in accent red with arrow
- Future case studies: add more inline sections following the same pattern

### 6. Contact

- "GOT AN IDEA?" — section heading scale
- Body text from copy
- `hello@ovmi.tech` — large (2-3rem), accent red, bold. Visual focal point.

### 7. Footer

- Thin horizontal rule (1px, muted)
- "2025 Ovmi.tech" + "Built by us. Like everything else."
- Small type, muted weight

## Technical Architecture

### Project Structure

```
ovmi-digital/
├── public/
│   ├── fonts/
│   │   └── Satoshi-Variable.woff2
│   ├── images/
│   │   ├── logo-mark.svg
│   │   ├── lola-pet-abstract.webp
│   │   └── og-image.png
│   └── favicon.svg
├── src/
│   ├── layouts/
│   │   └── Base.astro
│   ├── components/
│   │   ├── Hero.astro
│   │   ├── About.astro
│   │   ├── Services.astro
│   │   ├── Process.astro
│   │   ├── CaseStudy.astro
│   │   ├── Contact.astro
│   │   ├── Footer.astro
│   │   └── ThemeToggle.astro
│   ├── pages/
│   │   └── index.astro
│   └── styles/
│       └── global.css
├── astro.config.mjs
├── tailwind.config.mjs
├── package.json
└── tsconfig.json
```

### Key Decisions

- **100% static** — zero client JS except dark mode toggle (~10 lines inline)
- **Self-hosted font** — Satoshi Variable WOFF2, preloaded, no external requests
- **Tailwind v4** with CSS-first config
- **Dark mode** — `class` strategy on `<html>`, toggle persisted to `localStorage`
- **Images** — WebP, lazy loading, explicit width/height
- **SEO** — meta tags, Open Graph image, Organization structured data
- **Performance** — target 100/100 Lighthouse, <50KB CSS+JS

### Asset Generation (Replicate)

1. **Logo mark** — minimal geometric mark, traced to SVG
2. **Lola.pet abstract** — abstract art, warm/organic tones, pet/nature themed
3. **OG image** — "OVMI.DIGITAL" on black background, bold Satoshi type

## Dark Mode Behavior

- Toggle in top-right corner of hero, persists to localStorage
- Respects `prefers-color-scheme` on first visit
- Colors swap per the token table above
- Images: no treatment needed (abstract art works on both backgrounds)
- Accent shifts from `#E63946` to `#F87171` for WCAG contrast on dark backgrounds
