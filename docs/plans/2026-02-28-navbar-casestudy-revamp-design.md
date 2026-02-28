# Navbar & Case Study Revamp Design

## Overview

Two changes to the OVMI.DIGITAL landing page:

1. **Right-side navigation rail** — fixed vertical column on desktop with section links, theme toggle, and email. Hamburger menu on mobile.
2. **Case study revamp** — side-by-side text/image layout with alternating image position per case study, replacing the current full-bleed image banner.

## 1. Right-Side Navigation Rail

### Desktop (lg: 1024px+)

Fixed vertical column on the right edge, full viewport height, ~80px wide. Three zones stacked top-to-bottom:

1. **Theme toggle** — top of the rail (moved from current fixed top-right position)
2. **Section links** — vertically centered: About, Services, Process, Work, Contact
3. **Email** — bottom of the rail: `hello@ovmi.tech` (rotated or small text)

**Styling:**
- Links: 0.75rem, uppercase, letter-spacing 0.15em, muted color
- Active section: accent color, tracked via `IntersectionObserver`
- Clicking smooth-scrolls to section (uses existing `scroll-behavior: smooth`)
- Main content gets `lg:pr-20` (or similar) to avoid overlap with the rail

**Component:** New `SideNav.astro` component, replaces `ThemeToggle.astro` on desktop.

### Mobile (<1024px)

- Nav rail hidden entirely
- Hamburger icon: fixed top-right, same position/size as current theme toggle
- Opens full-screen overlay with:
  - Section links (large, centered, stacked)
  - Email link
  - Theme toggle
  - Close button (X) in hamburger position
- Overlay: matches current color mode (bg/text colors), fade transition
- Body scroll locked while overlay is open

**JS budget:** IntersectionObserver for active section tracking + hamburger open/close/scroll-lock. Inline script, no framework.

## 2. Case Study Revamp

### Layout

Side-by-side split replacing the full-bleed image banner:

- **Desktop:** Two columns, ~55% text / ~45% image
- **Alternation:** Odd case studies (1st, 3rd, ...) = text left, image right. Even (2nd, 4th, ...) = image left, text right.
- **Image:** Single featured image per case study, `object-cover`, fills column height, `rounded-lg`
- **Mobile (<768px):** Stacks to single column — image on top, text below. No alternation.

### Per Case Study

**Text side:**
- Project name: large, black weight (`clamp(2rem, 4vw, 3.5rem)`)
- Tagline: accent red, bold
- Structured sections: uppercase label + body text (same as current)
- External link: accent red with arrow

**Image side:**
- Single image, `object-cover`, rounded corners
- Fills the height of the text content (or a min-height)
- Lazy loaded

### Section Label

"OUR WORK" heading stays above the first case study, full-width, same style as other section labels.

### Spacing

Between case studies: `clamp(4rem, 8vh, 6rem)` vertical gap. No dividers — the alternating layout creates rhythm.

### Component Changes

`CaseStudy.astro` updated props:
```typescript
interface Props {
  name: string;
  tagline: string;
  imageSrc: string;
  imageAlt: string;
  url?: string;          // optional for placeholder entries
  sections: { label: string; content: string }[];
  imagePosition?: "left" | "right";  // defaults to "right"
  placeholder?: boolean;  // muted styling, no link
}
```

### Placeholder Case Study

A second entry to show the alternating pattern:
- Name: "Coming soon"
- Tagline: "Next project in the works"
- Image: Replicate-generated abstract (different palette from Lola.pet)
- No sections, no link
- Muted styling (lower opacity or different treatment)

## Technical Notes

- **Zero new dependencies** — all CSS + inline JS
- **IntersectionObserver** — lightweight, no scroll event listeners
- **Scroll lock** — `overflow: hidden` on `<html>` when mobile menu is open
- **Dark mode** — all new elements use existing CSS custom properties
- **Existing `ThemeToggle.astro`** — absorbed into the new `SideNav.astro` component; the standalone component is removed
