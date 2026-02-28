# OVMI.DIGITAL Landing Page Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Build a Swiss/Brutalist single-page portfolio site for OVMI.DIGITAL using Astro + Tailwind CSS v4 + Bun.

**Architecture:** Static single-page site with zero client JS (except ~10-line dark mode toggle). Astro components for each section, Tailwind v4 via `@tailwindcss/vite` plugin, self-hosted Satoshi variable font. Replicate for asset generation (logo, case study image, OG image).

**Tech Stack:** Astro 5.x, Tailwind CSS v4, Bun, Satoshi font (Fontshare), Replicate (flux-schnell)

**Design Doc:** `docs/plans/2026-02-28-ovmi-landing-page-design.md`

---

### Task 1: Scaffold Astro project with Bun

**Files:**
- Create: `package.json`, `astro.config.mjs`, `tsconfig.json`, `src/pages/index.astro`

**Step 1: Initialize Astro project**

Run: `cd /Users/ovee/dev/micovi/ovmi-digital && bun create astro@latest . --template minimal --install --no-git --typescript strict`

Accept defaults. If prompted about existing files, allow overwrite (only `ovmi-website-copy.md` and `docs/` exist).

**Step 2: Add Tailwind CSS v4**

Run: `bun add tailwindcss @tailwindcss/vite`

**Step 3: Configure Astro with Tailwind vite plugin**

Edit `astro.config.mjs`:

```js
import { defineConfig } from "astro/config";
import tailwindcss from "@tailwindcss/vite";

export default defineConfig({
  vite: {
    plugins: [tailwindcss()],
  },
});
```

**Step 4: Create global CSS with Tailwind import**

Create `src/styles/global.css`:

```css
@import "tailwindcss";
```

**Step 5: Verify dev server starts**

Run: `bun run dev`
Expected: Astro dev server starts on localhost:4321 without errors.

**Step 6: Commit**

```bash
git init
git add -A
git commit -m "feat: scaffold Astro project with Tailwind CSS v4 and Bun"
```

---

### Task 2: Set up Satoshi font and global styles

**Files:**
- Create: `public/fonts/Satoshi-Variable.woff2`, `public/fonts/Satoshi-VariableItalic.woff2`
- Modify: `src/styles/global.css`

**Step 1: Download Satoshi variable font**

Download from https://www.fontshare.com/fonts/satoshi — get the "Variable" WOFF2 files. Place them in `public/fonts/`:
- `Satoshi-Variable.woff2`
- `Satoshi-VariableItalic.woff2`

If the download provides a zip, extract and find the WOFF2 variable files. Alternatively, download from CDNFonts or a similar source.

**Step 2: Configure global CSS with font-face, dark mode, and design tokens**

Replace `src/styles/global.css` with:

```css
@import "tailwindcss";

@custom-variant dark (&:where(.dark, .dark *));

@font-face {
  font-family: "Satoshi";
  src: url("/fonts/Satoshi-Variable.woff2") format("woff2");
  font-weight: 300 900;
  font-display: swap;
  font-style: normal;
}

@font-face {
  font-family: "Satoshi";
  src: url("/fonts/Satoshi-VariableItalic.woff2") format("woff2");
  font-weight: 300 900;
  font-display: swap;
  font-style: italic;
}

@theme {
  --font-sans: "Satoshi", system-ui, sans-serif;

  --color-bg: #FAFAFA;
  --color-text: #000000;
  --color-accent: #E63946;
  --color-muted: #6B7280;

  --color-bg-dark: #0A0A0A;
  --color-text-dark: #FAFAFA;
  --color-accent-dark: #F87171;
  --color-muted-dark: #9CA3AF;
}

body {
  font-family: var(--font-sans);
  background-color: var(--color-bg);
  color: var(--color-text);
  font-size: 1.125rem;
  line-height: 1.6;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

.dark body {
  background-color: var(--color-bg-dark);
  color: var(--color-text-dark);
}

::selection {
  background-color: var(--color-accent);
  color: white;
}

.dark ::selection {
  background-color: var(--color-accent-dark);
  color: black;
}
```

**Step 3: Verify font loads in dev server**

Update `src/pages/index.astro` to import the CSS and show test text:

```astro
---
import "../styles/global.css";
---
<html lang="en">
  <head><meta charset="utf-8" /><title>OVMI.DIGITAL</title></head>
  <body>
    <h1 class="text-6xl font-black">We make things exist.</h1>
    <p class="font-normal">Testing Satoshi font loading.</p>
  </body>
</html>
```

Run: `bun run dev` — verify Satoshi renders at localhost:4321.

**Step 4: Commit**

```bash
git add public/fonts/ src/styles/global.css src/pages/index.astro
git commit -m "feat: add Satoshi variable font and global styles with dark mode tokens"
```

---

### Task 3: Build Base layout

**Files:**
- Create: `src/layouts/Base.astro`
- Modify: `src/pages/index.astro`

**Step 1: Create Base.astro layout**

Create `src/layouts/Base.astro`:

```astro
---
import "../styles/global.css";

interface Props {
  title?: string;
  description?: string;
}

const {
  title = "OVMI.DIGITAL — We make things exist.",
  description = "Ovmi is a small studio based in Europe. We build software, ship products, and integrate AI into everything we do.",
} = Astro.props;
---
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <meta name="description" content={description} />
    <title>{title}</title>

    <link rel="preload" href="/fonts/Satoshi-Variable.woff2" as="font" type="font/woff2" crossorigin />

    <meta property="og:title" content={title} />
    <meta property="og:description" content={description} />
    <meta property="og:type" content="website" />
    <meta property="og:url" content="https://ovmi.digital" />
    <meta property="og:image" content="https://ovmi.digital/images/og-image.png" />
    <meta name="twitter:card" content="summary_large_image" />

    <script is:inline>
      if (localStorage.getItem("theme") === "dark" ||
        (!localStorage.getItem("theme") && window.matchMedia("(prefers-color-scheme: dark)").matches)) {
        document.documentElement.classList.add("dark");
      }
    </script>
  </head>
  <body class="min-h-screen">
    <slot />
  </body>
</html>
```

Key points:
- Font preload for zero FOUT
- Dark mode script runs inline before paint (prevents flash)
- OG meta tags ready for generated image
- `is:inline` prevents Astro from bundling the script

**Step 2: Update index.astro to use layout**

```astro
---
import Base from "../layouts/Base.astro";
---
<Base>
  <h1 class="text-6xl font-black p-20">We make things exist.</h1>
</Base>
```

**Step 3: Verify**

Run: `bun run dev` — page loads with correct meta tags, font preloaded, dark mode script in `<head>`.

**Step 4: Commit**

```bash
git add src/layouts/Base.astro src/pages/index.astro
git commit -m "feat: add Base layout with SEO meta, font preload, and dark mode init"
```

---

### Task 4: Build ThemeToggle component

**Files:**
- Create: `src/components/ThemeToggle.astro`

**Step 1: Create ThemeToggle.astro**

```astro
---
---
<button
  id="theme-toggle"
  type="button"
  aria-label="Toggle dark mode"
  class="fixed top-6 right-6 z-50 w-10 h-10 flex items-center justify-center
         text-[var(--color-muted)] hover:text-[var(--color-text)]
         dark:text-[var(--color-muted-dark)] dark:hover:text-[var(--color-text-dark)]
         transition-colors cursor-pointer"
>
  <svg class="dark:hidden" width="20" height="20" viewBox="0 0 20 20" fill="none" stroke="currentColor" stroke-width="1.5">
    <circle cx="10" cy="10" r="4" />
    <path d="M10 2v2M10 16v2M2 10h2M16 10h2M4.93 4.93l1.41 1.41M13.66 13.66l1.41 1.41M4.93 15.07l1.41-1.41M13.66 6.34l1.41-1.41" />
  </svg>
  <svg class="hidden dark:block" width="20" height="20" viewBox="0 0 20 20" fill="none" stroke="currentColor" stroke-width="1.5">
    <path d="M17.293 13.293A8 8 0 016.707 2.707a8.003 8.003 0 1010.586 10.586z" />
  </svg>
</button>

<script is:inline>
  document.getElementById("theme-toggle").addEventListener("click", () => {
    const html = document.documentElement;
    const isDark = html.classList.toggle("dark");
    localStorage.setItem("theme", isDark ? "dark" : "light");
  });
</script>
```

**Step 2: Add to index.astro temporarily to verify**

```astro
---
import Base from "../layouts/Base.astro";
import ThemeToggle from "../components/ThemeToggle.astro";
---
<Base>
  <ThemeToggle />
  <h1 class="text-6xl font-black p-20">We make things exist.</h1>
</Base>
```

**Step 3: Verify**

Run: `bun run dev` — toggle button visible top-right, clicking it toggles dark/light, persists on refresh.

**Step 4: Commit**

```bash
git add src/components/ThemeToggle.astro src/pages/index.astro
git commit -m "feat: add dark mode toggle with localStorage persistence"
```

---

### Task 5: Build Hero component

**Files:**
- Create: `src/components/Hero.astro`

**Step 1: Create Hero.astro**

```astro
---
---
<section class="min-h-screen flex flex-col justify-between px-6 md:px-12 lg:px-20 py-8">
  <div class="flex items-center gap-3">
    <img src="/images/logo-mark.svg" alt="" class="h-8 w-8 dark:invert" />
    <span class="text-sm font-bold tracking-wide uppercase">OVMI.DIGITAL</span>
  </div>

  <div class="max-w-4xl">
    <h1
      class="font-black leading-[0.95] tracking-tight"
      style="font-size: clamp(3rem, 8vw, 8rem);"
    >
      We make things<br />exist.
    </h1>
    <p class="mt-6 text-lg font-normal max-w-lg opacity-70">
      We're builders first. Your project is in good hands.
    </p>
  </div>

  <a
    href="#work"
    class="text-[var(--color-accent)] dark:text-[var(--color-accent-dark)]
           font-bold text-sm tracking-wide uppercase hover:opacity-70 transition-opacity"
  >
    View our work &darr;
  </a>
</section>
```

Key points:
- `min-h-screen` with flex column and `justify-between` spaces logo/headline/CTA vertically
- Logo uses `dark:invert` as a temporary treatment until we have a proper SVG
- Headline uses inline `clamp()` for fluid sizing (Tailwind doesn't have a built-in for this exact range)
- `leading-[0.95]` tight line height for the massive headline — brutalist touch
- CTA uses CSS variable for accent color

**Step 2: Temporarily add a placeholder logo**

Create `public/images/logo-mark.svg` as a simple placeholder (will be replaced by Replicate-generated logo):

```svg
<svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" viewBox="0 0 32 32">
  <rect width="32" height="32" fill="black"/>
</svg>
```

**Step 3: Wire into index.astro and verify**

**Step 4: Commit**

```bash
git add src/components/Hero.astro public/images/logo-mark.svg
git commit -m "feat: add Hero section with fluid typography and accent CTA"
```

---

### Task 6: Build About component

**Files:**
- Create: `src/components/About.astro`

**Step 1: Create About.astro**

```astro
---
---
<section class="px-6 md:px-12 lg:px-20" style="padding-top: clamp(6rem, 12vh, 10rem); padding-bottom: clamp(6rem, 12vh, 10rem);">
  <p class="text-sm font-bold tracking-[0.2em] uppercase mb-12
            text-[var(--color-muted)] dark:text-[var(--color-muted-dark)]">
    About
  </p>
  <div class="max-w-2xl space-y-6">
    <p>Ovmi is a small studio based in Europe.</p>
    <p>
      We've spent over a decade building software, shipping products, and working
      at the edge of what technology can do — from blockchain infrastructure to
      full-stack applications. When AI changed everything, we didn't pivot. We
      doubled down.
    </p>
    <p>
      Today our team is a mix of engineers, designers, and AI agents working
      together on the same problems. We build our own products. We take on select
      client work. We care about craft.
    </p>
    <p class="font-bold text-xl mt-12">
      Small by choice. Serious by default.
    </p>
  </div>
</section>
```

**Step 2: Wire into index.astro and verify**

**Step 3: Commit**

```bash
git add src/components/About.astro
git commit -m "feat: add About section"
```

---

### Task 7: Build Services component

**Files:**
- Create: `src/components/Services.astro`

**Step 1: Create Services.astro**

```astro
---
const services = [
  {
    title: "Product development",
    description: "Web and mobile applications, built to last.",
  },
  {
    title: "AI integration & automation",
    description: "We embed AI into products and workflows. From simple automations to full agent systems.",
  },
  {
    title: "AI consultation",
    description: "Not sure where AI fits in your business? We'll help you find out — and build a plan that makes sense.",
  },
  {
    title: "Design",
    description: "Interfaces that work. Aesthetics that hold up.",
  },
  {
    title: "Blockchain development",
    description: "When the project calls for it, we know how to build it right.",
  },
];
---
<section class="px-6 md:px-12 lg:px-20" style="padding-top: clamp(6rem, 12vh, 10rem); padding-bottom: clamp(6rem, 12vh, 10rem);">
  <p class="text-sm font-bold tracking-[0.2em] uppercase mb-6
            text-[var(--color-muted)] dark:text-[var(--color-muted-dark)]">
    What We Do
  </p>
  <p class="max-w-2xl mb-16">
    Most clients come to us with an idea or a problem. We help figure out the rest.
  </p>
  <p class="max-w-2xl mb-16 text-[var(--color-muted)] dark:text-[var(--color-muted-dark)]">
    We work across the full stack — web, mobile, design, automation, blockchain,
    and AI. Not as separate services. As one connected capability.
  </p>
  <div class="grid grid-cols-1 md:grid-cols-2 gap-x-16 gap-y-12 max-w-4xl">
    {services.map((service) => (
      <div>
        <h3 class="font-bold text-lg mb-2">{service.title}</h3>
        <p class="text-[var(--color-muted)] dark:text-[var(--color-muted-dark)]">
          {service.description}
        </p>
      </div>
    ))}
  </div>
</section>
```

**Step 2: Wire into index.astro and verify**

**Step 3: Commit**

```bash
git add src/components/Services.astro
git commit -m "feat: add Services section with 2-column grid"
```

---

### Task 8: Build Process component

**Files:**
- Create: `src/components/Process.astro`

**Step 1: Create Process.astro**

```astro
---
const steps = [
  {
    title: "Conversation",
    description: "Every project starts with a conversation. No forms, no briefs — just a discussion about your idea, your problem, and where you want to go.",
  },
  {
    title: "Research",
    description: "We dig into the space, understand the real pain points, and come back with a clear perspective on what's worth building.",
  },
  {
    title: "Alignment",
    description: "Features, scope, priorities — nothing gets built without alignment.",
  },
  {
    title: "Build",
    description: "Through development we keep you close. Early versions, feedback loops, real conversations. No surprises at the finish line.",
  },
  {
    title: "Launch",
    description: "We help you test, release, and get to market. Then we step back — unless you need us to stay.",
  },
];
---
<section class="px-6 md:px-12 lg:px-20" style="padding-top: clamp(6rem, 12vh, 10rem); padding-bottom: clamp(6rem, 12vh, 10rem);">
  <p class="text-sm font-bold tracking-[0.2em] uppercase mb-16
            text-[var(--color-muted)] dark:text-[var(--color-muted-dark)]">
    How We Work
  </p>
  <div class="max-w-3xl space-y-12">
    {steps.map((step, i) => (
      <div class="flex gap-8">
        <span
          class="text-2xl font-black text-[var(--color-accent)] dark:text-[var(--color-accent-dark)]
                 shrink-0 w-12"
        >
          {String(i + 1).padStart(2, "0")}
        </span>
        <div>
          <h3 class="font-bold text-lg mb-2">{step.title}</h3>
          <p class="text-[var(--color-muted)] dark:text-[var(--color-muted-dark)]">
            {step.description}
          </p>
        </div>
      </div>
    ))}
  </div>
</section>
```

**Step 2: Wire into index.astro and verify**

**Step 3: Commit**

```bash
git add src/components/Process.astro
git commit -m "feat: add Process section with numbered steps"
```

---

### Task 9: Build CaseStudy component

**Files:**
- Create: `src/components/CaseStudy.astro`

**Step 1: Create CaseStudy.astro**

```astro
---
interface Props {
  name: string;
  tagline: string;
  imageSrc: string;
  imageAlt: string;
  url: string;
  sections: { label: string; content: string }[];
}

const { name, tagline, imageSrc, imageAlt, url, sections } = Astro.props;
---
<article id="work">
  <div class="w-full" style="height: clamp(20rem, 50vh, 36rem);">
    <img
      src={imageSrc}
      alt={imageAlt}
      class="w-full h-full object-cover"
      loading="lazy"
      decoding="async"
    />
  </div>

  <div class="px-6 md:px-12 lg:px-20 mt-12">
    <h2 class="font-black" style="font-size: clamp(2rem, 4vw, 3.5rem);">
      {name}
    </h2>
    <p class="text-[var(--color-accent)] dark:text-[var(--color-accent-dark)] font-bold mt-2">
      {tagline}
    </p>

    <div class="max-w-2xl mt-12 space-y-10">
      {sections.map((section) => (
        <div>
          <p class="text-sm font-bold tracking-[0.2em] uppercase mb-4
                    text-[var(--color-muted)] dark:text-[var(--color-muted-dark)]">
            {section.label}
          </p>
          <p>{section.content}</p>
        </div>
      ))}
    </div>

    <a
      href={url}
      target="_blank"
      rel="noopener noreferrer"
      class="inline-block mt-10 text-[var(--color-accent)] dark:text-[var(--color-accent-dark)]
             font-bold hover:opacity-70 transition-opacity"
    >
      &rarr; {url.replace("https://", "")}
    </a>
  </div>
</article>
```

Key points:
- Image is full-bleed (no horizontal padding on the image container)
- Content below has the standard page padding
- Reusable for future case studies — pass props
- Lazy-loaded image

**Step 2: Wire into index.astro with Lola.pet data**

In `index.astro`, use the component with placeholder image for now:

```astro
<CaseStudy
  name="Lola.pet"
  tagline="Pet sitting, done right."
  imageSrc="/images/lola-pet-abstract.webp"
  imageAlt="Abstract artwork representing Lola.pet"
  url="https://lola.pet"
  sections={[
    {
      label: "The project",
      content: "We bought the domain for two reasons. It was perfect. And one of us has a Labrador named Lola. The platform existed. The idea was solid. But the product needed to grow up.",
    },
    {
      label: "What we built",
      content: "We revamped Lola from the ground up — keeping what worked, replacing what didn't. No more invite gates. Anyone can sign up, find a verified sitter or dog walker, and book — all within the platform. Payments and communication happen in one place. Service providers go through identity verification before their first booking. We also built for the broader community. Pet-friendly places across Romania, public adoption listings, and resources for pet owners who need more than just a sitter.",
    },
    {
      label: "Where it's going",
      content: "Lola launches in Romania this year. 500+ people already on the waitlist. This is what we do — we find the right idea, build it properly, and ship it.",
    },
  ]}
/>
```

Create a placeholder image at `public/images/lola-pet-abstract.webp` — use a solid gray block or a temporary image until Task 12 generates the real one.

**Step 3: Verify layout**

**Step 4: Commit**

```bash
git add src/components/CaseStudy.astro
git commit -m "feat: add reusable CaseStudy component with full-bleed imagery"
```

---

### Task 10: Build Contact component

**Files:**
- Create: `src/components/Contact.astro`

**Step 1: Create Contact.astro**

```astro
---
---
<section class="px-6 md:px-12 lg:px-20" style="padding-top: clamp(6rem, 12vh, 10rem); padding-bottom: clamp(6rem, 12vh, 10rem);">
  <h2 class="font-black" style="font-size: clamp(2rem, 4vw, 3.5rem);">
    Got an idea?
  </h2>
  <div class="max-w-2xl mt-8 space-y-6">
    <p>Tell us about it.</p>
    <p class="text-[var(--color-muted)] dark:text-[var(--color-muted-dark)]">
      We don't do long forms or sales calls. Just a straightforward conversation
      about what you're building and whether we're the right fit.
    </p>
  </div>
  <a
    href="mailto:hello@ovmi.tech"
    class="inline-block mt-12 font-bold text-[var(--color-accent)] dark:text-[var(--color-accent-dark)]
           hover:opacity-70 transition-opacity"
    style="font-size: clamp(1.5rem, 3vw, 2.5rem);"
  >
    hello@ovmi.tech
  </a>
</section>
```

**Step 2: Wire into index.astro and verify**

**Step 3: Commit**

```bash
git add src/components/Contact.astro
git commit -m "feat: add Contact section with mailto CTA"
```

---

### Task 11: Build Footer component

**Files:**
- Create: `src/components/Footer.astro`

**Step 1: Create Footer.astro**

```astro
---
---
<footer class="px-6 md:px-12 lg:px-20 pb-8 pt-16">
  <hr class="border-[var(--color-muted)]/20 dark:border-[var(--color-muted-dark)]/20 mb-8" />
  <div class="flex flex-col sm:flex-row sm:justify-between gap-2
              text-sm text-[var(--color-muted)] dark:text-[var(--color-muted-dark)]">
    <p>&copy; {new Date().getFullYear()} Ovmi.tech</p>
    <p>Built by us. Like everything else.</p>
  </div>
</footer>
```

**Step 2: Wire into index.astro and verify**

**Step 3: Commit**

```bash
git add src/components/Footer.astro
git commit -m "feat: add Footer component"
```

---

### Task 12: Assemble index.astro (all sections)

**Files:**
- Modify: `src/pages/index.astro`

**Step 1: Assemble all components in order**

```astro
---
import Base from "../layouts/Base.astro";
import ThemeToggle from "../components/ThemeToggle.astro";
import Hero from "../components/Hero.astro";
import About from "../components/About.astro";
import Services from "../components/Services.astro";
import Process from "../components/Process.astro";
import CaseStudy from "../components/CaseStudy.astro";
import Contact from "../components/Contact.astro";
import Footer from "../components/Footer.astro";
---
<Base>
  <ThemeToggle />
  <Hero />
  <About />
  <Services />
  <Process />

  <section class="px-6 md:px-12 lg:px-20" style="padding-top: clamp(6rem, 12vh, 10rem);">
    <p class="text-sm font-bold tracking-[0.2em] uppercase mb-16
              text-[var(--color-muted)] dark:text-[var(--color-muted-dark)]">
      Our Work
    </p>
  </section>

  <CaseStudy
    name="Lola.pet"
    tagline="Pet sitting, done right."
    imageSrc="/images/lola-pet-abstract.webp"
    imageAlt="Abstract artwork representing Lola.pet"
    url="https://lola.pet"
    sections={[
      {
        label: "The project",
        content: "We bought the domain for two reasons. It was perfect. And one of us has a Labrador named Lola. The platform existed. The idea was solid. But the product needed to grow up.",
      },
      {
        label: "What we built",
        content: "We revamped Lola from the ground up — keeping what worked, replacing what didn't. No more invite gates. Anyone can sign up, find a verified sitter or dog walker, and book — all within the platform. Payments and communication happen in one place. Service providers go through identity verification before their first booking. We also built for the broader community. Pet-friendly places across Romania, public adoption listings, and resources for pet owners who need more than just a sitter.",
      },
      {
        label: "Where it's going",
        content: "Lola launches in Romania this year. 500+ people already on the waitlist. This is what we do — we find the right idea, build it properly, and ship it.",
      },
    ]}
  />

  <Contact />
  <Footer />
</Base>
```

**Step 2: Full visual review in dev server**

Run: `bun run dev` — scroll through all sections, test dark mode toggle, check responsive at mobile/tablet/desktop.

**Step 3: Commit**

```bash
git add src/pages/index.astro
git commit -m "feat: assemble all sections into landing page"
```

---

### Task 13: Generate assets via Replicate

**Files:**
- Create/replace: `public/images/lola-pet-abstract.webp`, `public/images/logo-mark.svg`, `public/images/og-image.png`

This task uses the Replicate MCP server with `black-forest-labs/flux-schnell`.

**Step 1: Generate Lola.pet abstract image**

Use Replicate to run flux-schnell with prompt:

```
Abstract art, warm organic tones, soft amber and terracotta on cream white background, fluid organic shapes suggesting nature and companionship, minimal composition, high contrast, editorial quality, no text, no animals, purely abstract
```

Parameters: width 1440, height 810, aspect ratio 16:9

Download the output and convert to WebP. Save as `public/images/lola-pet-abstract.webp`.

**Step 2: Generate logo mark**

Use Replicate with prompt:

```
Minimal geometric logo mark, single abstract shape, black on white background, clean edges, modernist design, simple enough to work at 32x32px, no text, no letters, pure geometry
```

Parameters: width 512, height 512

Download, trace to SVG using an online tool or `potrace`, clean up, save as `public/images/logo-mark.svg`.

**Step 3: Generate OG image**

Use Replicate or create programmatically: black background, "OVMI.DIGITAL" in white bold Satoshi text, centered. This can also be created with a simple HTML-to-image approach or designed in Figma. Save as `public/images/og-image.png` at 1200x630.

**Step 4: Create favicon**

Create `public/favicon.svg` — use the logo mark or a simple geometric shape:

```svg
<svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" viewBox="0 0 32 32">
  <rect width="32" height="32" rx="4" fill="black"/>
  <text x="16" y="22" text-anchor="middle" fill="white"
        font-family="system-ui" font-weight="900" font-size="16">O</text>
</svg>
```

Update `Base.astro` to include: `<link rel="icon" type="image/svg+xml" href="/favicon.svg" />`

**Step 5: Verify all images render**

Run: `bun run dev` — check hero logo, case study image, dark mode invert behavior.

**Step 6: Commit**

```bash
git add public/images/ public/favicon.svg src/layouts/Base.astro
git commit -m "feat: add generated logo, case study image, OG image, and favicon"
```

---

### Task 14: Add structured data and final SEO

**Files:**
- Modify: `src/layouts/Base.astro`

**Step 1: Add Organization JSON-LD**

Add before closing `</head>` in `Base.astro`:

```astro
<script type="application/ld+json" set:html={JSON.stringify({
  "@context": "https://schema.org",
  "@type": "Organization",
  "name": "OVMI.DIGITAL",
  "url": "https://ovmi.digital",
  "logo": "https://ovmi.digital/images/logo-mark.svg",
  "email": "hello@ovmi.tech",
  "description": "Small European studio building software, shipping products, and integrating AI.",
  "foundingDate": "2025",
  "areaServed": "Worldwide",
})} />
```

**Step 2: Add canonical URL and remaining meta**

```html
<link rel="canonical" href="https://ovmi.digital" />
<meta name="robots" content="index, follow" />
```

**Step 3: Verify with View Source**

**Step 4: Commit**

```bash
git add src/layouts/Base.astro
git commit -m "feat: add Organization structured data and canonical URL"
```

---

### Task 15: Production build and Lighthouse audit

**Files:** None (verification only)

**Step 1: Run production build**

Run: `bun run build`
Expected: Clean build with no warnings. Output in `dist/`.

**Step 2: Preview production build**

Run: `bun run preview`
Check: All pages render, images load, dark mode works.

**Step 3: Check bundle size**

Run: `ls -la dist/` and check CSS/JS output sizes.
Target: <50KB total for CSS + JS.

**Step 4: Run Lighthouse audit**

Open Chrome DevTools > Lighthouse on the preview URL (localhost:4321).
Target: 100 Performance, 100 Accessibility, 100 Best Practices, 100 SEO.

Fix any issues found.

**Step 5: Final commit**

```bash
git add -A
git commit -m "chore: verify production build and Lighthouse scores"
```

---

## Task Dependency Graph

```
Task 1 (scaffold) → Task 2 (fonts/styles) → Task 3 (Base layout) → Task 4 (ThemeToggle)
                                                                  ↘
Task 5 (Hero) ──────────────────────────────────────────────────────→ Task 12 (assemble)
Task 6 (About) ────────────────────────────────────────────────────→ Task 12
Task 7 (Services) ─────────────────────────────────────────────────→ Task 12
Task 8 (Process) ──────────────────────────────────────────────────→ Task 12
Task 9 (CaseStudy) ───────────────────────────────────────────────→ Task 12
Task 10 (Contact) ────────────────────────────────────────────────→ Task 12
Task 11 (Footer) ─────────────────────────────────────────────────→ Task 12

Task 12 (assemble) → Task 13 (assets) → Task 14 (SEO) → Task 15 (build + audit)
```

Tasks 5–11 (section components) can be built in **parallel** after Task 4 completes.
Task 13 (asset generation) can also start in parallel with component work but must complete before Task 15.
