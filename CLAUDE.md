# OVMI.DIGITAL

Company portfolio site for Ovmi, a small European software studio.

## Stack

- **Framework:** Astro (static, zero client JS except dark mode toggle)
- **CSS:** Tailwind v4 with `@theme` directive, CSS-first config
- **Runtime:** Bun
- **Font:** Satoshi (variable, self-hosted WOFF2)
- **Domain:** ovmi.digital

## Project Structure

```
src/
├── layouts/Base.astro        # HTML shell, meta tags, dark mode init
├── pages/index.astro         # Single-page layout, composes all sections
├── components/
│   ├── Hero.astro            # Full-viewport hero with headline
│   ├── SideNav.astro         # Desktop nav rail + mobile hamburger menu
│   ├── About.astro           # Studio description
│   ├── Services.astro        # 2-col grid of service offerings
│   ├── Process.astro         # Numbered 5-step workflow
│   ├── CaseStudy.astro       # Reusable case study with image + sections
│   ├── Contact.astro         # CTA with email link
│   ├── Footer.astro          # Copyright line
│   └── ThemeToggle.astro     # Standalone toggle (used by SideNav)
└── styles/global.css         # Tailwind import, @theme tokens, font-face
```

## Design System

Ovmi uses a token-first design system. See `docs/plans/2026-02-28-ovmi-design-system-design.md` for the full specification.

### Colors

| Token | Light | Dark |
|-------|-------|------|
| `--color-bg` | `#FAFAFA` | `#0A0A0A` |
| `--color-text` | `#000000` | `#FAFAFA` |
| `--color-accent` | `#E63946` | `#F87171` |
| `--color-muted` | `#6B7280` | `#9CA3AF` |

Accent is for interactive elements only. Never hardcode hex values — use `var(--color-*)`.

### Typography

- Display: `clamp(3rem, 8vw, 8rem)`, weight 900
- Section headings: `clamp(2rem, 4vw, 3.5rem)`, weight 900
- Body: `1.125rem`, weight 400, line-height 1.6
- Labels: uppercase, bold, `0.2em` letter-spacing, small size
- Max 3 weights per page: 400, 700, 900
- Body text max-width: 640px

### Spacing

- Section vertical padding: `clamp(6rem, 12vh, 10rem)`
- Horizontal padding: `px-6 md:px-12 lg:px-20`
- Content max-width: 72rem (1152px)

### Dark Mode

- Class-based on `<html>` element (`.dark`)
- Inline `<script>` in `<head>` prevents flash
- Respects `prefers-color-scheme` on first visit, persists to `localStorage`
- Use CSS variables for colors, not Tailwind `dark:` prefix
- `dark:` acceptable for structural changes (`dark:invert`, `dark:hidden`)

### Visual Style

Swiss/Brutalist — typography-driven, aggressive whitespace, near-zero motion. No gradients, no decorative elements. Hover states use `opacity-70` transitions.

## Component Conventions

- All components are `.astro` files — no React/Vue/Svelte
- Props defined with TypeScript interfaces in frontmatter
- Data (services, process steps, nav links) defined as arrays in frontmatter
- Responsive: mobile-first with `md:` and `lg:` breakpoints
- SideNav is a fixed right-rail on desktop, hamburger overlay on mobile
- CaseStudy accepts `imagePosition: "left" | "right"` for alternating layouts

## Commands

```sh
bun run dev       # Start dev server
bun run build     # Build static site
bun run preview   # Preview production build
```

## Rules

- No client-side JavaScript beyond dark mode toggle and mobile menu
- No external requests — all fonts and images self-hosted
- Images: WebP format, lazy loaded, explicit dimensions
- SEO: every page needs meta description, OG tags, canonical URL
- Performance target: 100/100 Lighthouse, <50KB CSS+JS
- Copy voice: short sentences, no filler, no exclamation marks in headings
