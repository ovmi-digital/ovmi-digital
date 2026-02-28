# Navbar & Case Study Revamp Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Add a fixed right-side navigation rail (desktop) with hamburger menu (mobile) and revamp the case study layout from full-bleed image to alternating text/image split.

**Architecture:** Two independent features that share one integration point (the page layout). The SideNav component replaces ThemeToggle and adds section navigation + email. The CaseStudy component is rewritten for a two-column alternating layout. Both use only CSS + inline JS — no new dependencies.

**Tech Stack:** Astro 5.x, Tailwind CSS v4, Bun, Replicate (asset generation)

---

### Task 1: Add section IDs to all components

Currently only `CaseStudy.astro` has `id="work"`. The nav needs anchor targets on every section.

**Files:**
- Modify: `src/components/About.astro:3` — add `id="about"`
- Modify: `src/components/Services.astro:25` — add `id="services"`
- Modify: `src/components/Process.astro:25` — add `id="process"`
- Modify: `src/components/Contact.astro:3` — add `id="contact"`

**Step 1: Add IDs to each section element**

In `src/components/About.astro`, change line 3:
```html
<section id="about" class="px-6 md:px-12 lg:px-20" style="padding-top: clamp(6rem, 12vh, 10rem); padding-bottom: clamp(6rem, 12vh, 10rem);">
```

In `src/components/Services.astro`, change line 25:
```html
<section id="services" class="px-6 md:px-12 lg:px-20" style="padding-top: clamp(6rem, 12vh, 10rem); padding-bottom: clamp(6rem, 12vh, 10rem);">
```

In `src/components/Process.astro`, change line 25:
```html
<section id="process" class="px-6 md:px-12 lg:px-20" style="padding-top: clamp(6rem, 12vh, 10rem); padding-bottom: clamp(6rem, 12vh, 10rem);">
```

In `src/components/Contact.astro`, change line 3:
```html
<section id="contact" class="px-6 md:px-12 lg:px-20" style="padding-top: clamp(6rem, 12vh, 10rem); padding-bottom: clamp(6rem, 12vh, 10rem);">
```

**Step 2: Verify build passes**

Run: `bun run build`
Expected: Build succeeds with zero errors.

**Step 3: Commit**

```bash
git add src/components/About.astro src/components/Services.astro src/components/Process.astro src/components/Contact.astro
git commit -m "feat: add section IDs for nav anchor targets"
```

---

### Task 2: Create SideNav component (desktop rail)

Create the right-side fixed navigation rail visible on `lg:` (1024px+). This contains the theme toggle at top, section links centered, and email at bottom.

**Files:**
- Create: `src/components/SideNav.astro`

**Step 1: Create the component**

Create `src/components/SideNav.astro` with this content:

```astro
---
const navLinks = [
  { label: "About", href: "#about" },
  { label: "Services", href: "#services" },
  { label: "Process", href: "#process" },
  { label: "Work", href: "#work" },
  { label: "Contact", href: "#contact" },
];
---

<!-- Desktop nav rail -->
<nav
  class="fixed top-0 right-0 h-screen w-20 z-40
         hidden lg:flex flex-col items-center justify-between py-8"
  aria-label="Section navigation"
>
  <!-- Theme toggle -->
  <button
    id="theme-toggle"
    type="button"
    aria-label="Toggle dark mode"
    class="w-10 h-10 flex items-center justify-center
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

  <!-- Section links -->
  <ul class="flex flex-col items-center gap-6">
    {navLinks.map((link) => (
      <li>
        <a
          href={link.href}
          class="side-nav-link text-[0.65rem] font-bold uppercase tracking-[0.15em]
                 text-[var(--color-muted)] dark:text-[var(--color-muted-dark)]
                 hover:text-[var(--color-text)] dark:hover:text-[var(--color-text-dark)]
                 transition-colors"
          data-section={link.href.slice(1)}
        >
          {link.label}
        </a>
      </li>
    ))}
  </ul>

  <!-- Email -->
  <a
    href="mailto:hello@ovmi.tech"
    class="text-[0.6rem] font-bold tracking-wider
           text-[var(--color-muted)] dark:text-[var(--color-muted-dark)]
           hover:text-[var(--color-accent)] dark:hover:text-[var(--color-accent-dark)]
           transition-colors [writing-mode:vertical-rl] rotate-180"
  >
    hello@ovmi.tech
  </a>
</nav>

<!-- Mobile hamburger button -->
<button
  id="mobile-menu-toggle"
  type="button"
  aria-label="Open menu"
  class="fixed top-6 right-6 z-50 w-10 h-10
         flex lg:hidden items-center justify-center
         text-[var(--color-muted)] hover:text-[var(--color-text)]
         dark:text-[var(--color-muted-dark)] dark:hover:text-[var(--color-text-dark)]
         transition-colors cursor-pointer"
>
  <svg id="hamburger-icon" width="22" height="22" viewBox="0 0 22 22" fill="none" stroke="currentColor" stroke-width="1.5">
    <path d="M3 6h16M3 11h16M3 16h16" />
  </svg>
  <svg id="close-icon" class="hidden" width="22" height="22" viewBox="0 0 22 22" fill="none" stroke="currentColor" stroke-width="1.5">
    <path d="M5 5l12 12M17 5L5 17" />
  </svg>
</button>

<!-- Mobile overlay -->
<div
  id="mobile-menu"
  class="fixed inset-0 z-40 hidden
         bg-[var(--color-bg)] dark:bg-[var(--color-bg-dark)]
         flex-col items-center justify-center gap-8"
>
  <ul class="flex flex-col items-center gap-6">
    {navLinks.map((link) => (
      <li>
        <a
          href={link.href}
          class="mobile-nav-link text-2xl font-bold uppercase tracking-[0.1em]
                 text-[var(--color-text)] dark:text-[var(--color-text-dark)]
                 hover:text-[var(--color-accent)] dark:hover:text-[var(--color-accent-dark)]
                 transition-colors"
        >
          {link.label}
        </a>
      </li>
    ))}
  </ul>

  <a
    href="mailto:hello@ovmi.tech"
    class="text-sm font-bold
           text-[var(--color-accent)] dark:text-[var(--color-accent-dark)]
           hover:opacity-70 transition-opacity"
  >
    hello@ovmi.tech
  </a>

  <!-- Theme toggle in mobile menu -->
  <button
    id="mobile-theme-toggle"
    type="button"
    aria-label="Toggle dark mode"
    class="w-10 h-10 flex items-center justify-center
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
</div>

<script is:inline>
  // Theme toggle (both desktop and mobile buttons)
  function toggleTheme() {
    const isDark = document.documentElement.classList.toggle("dark");
    localStorage.setItem("theme", isDark ? "dark" : "light");
  }
  document.getElementById("theme-toggle").addEventListener("click", toggleTheme);
  document.getElementById("mobile-theme-toggle").addEventListener("click", toggleTheme);

  // Mobile menu open/close
  const menuToggle = document.getElementById("mobile-menu-toggle");
  const mobileMenu = document.getElementById("mobile-menu");
  const hamburgerIcon = document.getElementById("hamburger-icon");
  const closeIcon = document.getElementById("close-icon");

  function openMenu() {
    mobileMenu.classList.remove("hidden");
    mobileMenu.classList.add("flex");
    hamburgerIcon.classList.add("hidden");
    closeIcon.classList.remove("hidden");
    document.documentElement.style.overflow = "hidden";
    menuToggle.setAttribute("aria-label", "Close menu");
  }

  function closeMenu() {
    mobileMenu.classList.add("hidden");
    mobileMenu.classList.remove("flex");
    hamburgerIcon.classList.remove("hidden");
    closeIcon.classList.add("hidden");
    document.documentElement.style.overflow = "";
    menuToggle.setAttribute("aria-label", "Open menu");
  }

  menuToggle.addEventListener("click", () => {
    const isOpen = !mobileMenu.classList.contains("hidden");
    isOpen ? closeMenu() : openMenu();
  });

  // Close mobile menu when clicking a nav link
  mobileMenu.querySelectorAll(".mobile-nav-link").forEach((link) => {
    link.addEventListener("click", closeMenu);
  });

  // Active section tracking (desktop only)
  const sections = ["about", "services", "process", "work", "contact"];
  const navLinksEls = document.querySelectorAll(".side-nav-link");

  const observer = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting) {
          const id = entry.target.id;
          navLinksEls.forEach((link) => {
            if (link.dataset.section === id) {
              link.style.color = "";
              link.classList.remove(
                "text-[var(--color-muted)]",
                "dark:text-[var(--color-muted-dark)]"
              );
              link.classList.add(
                "text-[var(--color-accent)]",
                "dark:text-[var(--color-accent-dark)]"
              );
            } else {
              link.classList.remove(
                "text-[var(--color-accent)]",
                "dark:text-[var(--color-accent-dark)]"
              );
              link.classList.add(
                "text-[var(--color-muted)]",
                "dark:text-[var(--color-muted-dark)]"
              );
            }
          });
        }
      });
    },
    { rootMargin: "-40% 0px -40% 0px", threshold: 0 }
  );

  sections.forEach((id) => {
    const el = document.getElementById(id);
    if (el) observer.observe(el);
  });
</script>
```

**Step 2: Verify build passes**

Run: `bun run build`
Expected: Build succeeds.

**Step 3: Commit**

```bash
git add src/components/SideNav.astro
git commit -m "feat: add SideNav component with desktop rail and mobile hamburger"
```

---

### Task 3: Integrate SideNav and add content right padding

Replace ThemeToggle with SideNav in the page, and add right padding on desktop so content doesn't overlap the nav rail.

**Files:**
- Modify: `src/pages/index.astro:3,13` — swap ThemeToggle for SideNav
- Modify: `src/layouts/Base.astro:53` — add `lg:pr-20` to body
- Delete: `src/components/ThemeToggle.astro` — no longer needed

**Step 1: Update index.astro**

Replace the ThemeToggle import and usage:

Change line 3 from:
```astro
import ThemeToggle from "../components/ThemeToggle.astro";
```
to:
```astro
import SideNav from "../components/SideNav.astro";
```

Change line 13 from:
```astro
  <ThemeToggle />
```
to:
```astro
  <SideNav />
```

**Step 2: Add right padding to body for desktop**

In `src/layouts/Base.astro`, change line 53 from:
```html
    <body class="min-h-screen">
```
to:
```html
    <body class="min-h-screen lg:pr-20">
```

**Step 3: Delete ThemeToggle.astro**

```bash
rm src/components/ThemeToggle.astro
```

**Step 4: Verify build passes and test locally**

Run: `bun run build`
Expected: Build succeeds with zero errors.

Run: `bun run dev`
Manual check:
- Desktop (1440px): nav rail visible on right with theme toggle, 5 section links, email at bottom
- Mobile (390px): hamburger icon top-right, opens full-screen overlay, close works, scroll lock works
- Click section links: smooth-scrolls to correct section
- Active section highlighting changes as you scroll
- Theme toggle works in both desktop rail and mobile overlay
- Dark mode: all nav elements use correct dark tokens

**Step 5: Commit**

```bash
git add src/pages/index.astro src/layouts/Base.astro
git rm src/components/ThemeToggle.astro
git commit -m "feat: replace ThemeToggle with SideNav, add desktop content padding"
```

---

### Task 4: Revamp CaseStudy component for alternating layout

Rewrite the CaseStudy component to use a two-column side-by-side layout with configurable image position (left or right). Add support for placeholder entries.

**Files:**
- Modify: `src/components/CaseStudy.astro` — full rewrite

**Step 1: Rewrite CaseStudy.astro**

Replace the entire content of `src/components/CaseStudy.astro` with:

```astro
---
interface Props {
  name: string;
  tagline: string;
  imageSrc: string;
  imageAlt: string;
  url?: string;
  sections?: { label: string; content: string }[];
  imagePosition?: "left" | "right";
  placeholder?: boolean;
}

const {
  name,
  tagline,
  imageSrc,
  imageAlt,
  url,
  sections = [],
  imagePosition = "right",
  placeholder = false,
} = Astro.props;

const isLeft = imagePosition === "left";
---

<article class={`px-6 md:px-12 lg:px-20 ${placeholder ? "opacity-50" : ""}`}>
  <div class={`flex flex-col ${isLeft ? "md:flex-row-reverse" : "md:flex-row"} gap-8 md:gap-12 lg:gap-16`}>
    <!-- Text side -->
    <div class="md:w-[55%] flex flex-col justify-center">
      <h3 class="font-black" style="font-size: clamp(2rem, 4vw, 3.5rem);">
        {name}
      </h3>
      <p class={`font-bold mt-2 ${placeholder
        ? "text-[var(--color-muted)] dark:text-[var(--color-muted-dark)]"
        : "text-[var(--color-accent)] dark:text-[var(--color-accent-dark)]"
      }`}>
        {tagline}
      </p>

      {sections.length > 0 && (
        <div class="mt-10 space-y-8">
          {sections.map((section) => (
            <div>
              <p class="text-sm font-bold tracking-[0.2em] uppercase mb-3
                        text-[var(--color-muted)] dark:text-[var(--color-muted-dark)]">
                {section.label}
              </p>
              <p>{section.content}</p>
            </div>
          ))}
        </div>
      )}

      {url && !placeholder && (
        <a
          href={url}
          target="_blank"
          rel="noopener noreferrer"
          class="inline-block mt-8 text-[var(--color-accent)] dark:text-[var(--color-accent-dark)]
                 font-bold hover:opacity-70 transition-opacity"
        >
          &rarr; {url.replace("https://", "")}
        </a>
      )}
    </div>

    <!-- Image side -->
    <div class="md:w-[45%]" style="min-height: clamp(16rem, 40vh, 28rem);">
      <img
        src={imageSrc}
        alt={imageAlt}
        class="w-full h-full object-cover rounded-lg"
        loading="lazy"
        decoding="async"
      />
    </div>
  </div>
</article>
```

**Step 2: Verify build passes**

Run: `bun run build`
Expected: Build succeeds.

**Step 3: Commit**

```bash
git add src/components/CaseStudy.astro
git commit -m "feat: revamp CaseStudy with alternating text/image split layout"
```

---

### Task 5: Extract "Our Work" heading and update index.astro with case studies

Move the "Our Work" heading out of CaseStudy (it was baked in for the single-case-study layout) and into the page. Add the placeholder second case study to show the alternating pattern.

**Files:**
- Modify: `src/pages/index.astro:19-39` — add "Our Work" heading and second case study

**Step 1: Update index.astro**

Replace the CaseStudy usage (lines 19-39) with:

```astro
  <section id="work" class="pt-[clamp(6rem,12vh,10rem)]">
    <h2 class="text-sm font-bold tracking-[0.2em] uppercase mb-16 px-6 md:px-12 lg:px-20
              text-[var(--color-muted)] dark:text-[var(--color-muted-dark)]">
      Our Work
    </h2>

    <div class="space-y-[clamp(4rem,8vh,6rem)]">
      <CaseStudy
        name="Lola.pet"
        tagline="Pet sitting, done right."
        imageSrc="/images/lola-pet-abstract.webp"
        imageAlt="Abstract artwork representing Lola.pet"
        url="https://lola.pet"
        imagePosition="right"
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

      <CaseStudy
        name="Coming soon"
        tagline="Next project in the works."
        imageSrc="/images/coming-soon-abstract.webp"
        imageAlt="Abstract artwork for upcoming project"
        imagePosition="left"
        placeholder={true}
        sections={[]}
      />
    </div>
  </section>
```

**Step 2: Verify build passes**

Run: `bun run build`
Expected: Build succeeds. (The placeholder image doesn't exist yet — that's Task 6. The build will still pass; the image will be a broken link until generated.)

**Step 3: Commit**

```bash
git add src/pages/index.astro
git commit -m "feat: extract Our Work heading, add placeholder case study with alternating layout"
```

---

### Task 6: Generate placeholder case study image via Replicate

Generate an abstract image for the "Coming soon" placeholder case study. Different color palette from the Lola.pet warm/orange tones — use cool blues/purples/greens.

**Files:**
- Create: `public/images/coming-soon-abstract.webp` — Replicate-generated

**Step 1: Generate the image**

Use Replicate MCP server with `black-forest-labs/flux-schnell` model.

Prompt: `"abstract minimal geometric artwork, cool tones, deep blue and teal gradients, flowing organic shapes, clean modern aesthetic, white background, high contrast"` — 1344x768 (landscape to match the case study image area).

**Step 2: Save to public/images/**

Save the output as `public/images/coming-soon-abstract.webp`. If Replicate outputs PNG, convert:
```bash
cwebp -q 85 public/images/coming-soon-abstract.png -o public/images/coming-soon-abstract.webp
```

If `cwebp` is not available, keep it as `.webp` from Replicate or use the raw format and update the `imageSrc` in `index.astro` accordingly.

**Step 3: Verify build passes and image loads**

Run: `bun run build`
Run: `bun run dev`
Manual check: second case study shows the image on the left side, text on the right, muted styling.

**Step 4: Commit**

```bash
git add public/images/coming-soon-abstract.webp
git commit -m "feat: add Replicate-generated abstract image for placeholder case study"
```

---

### Task 7: Visual review and polish

Open the site in the browser and review at desktop (1440px) and mobile (390px), both light and dark modes. Fix any spacing, alignment, or dark mode issues.

**Files:**
- Potentially modify: any component file based on review findings

**Step 1: Run dev server and review**

Run: `bun run dev`

Check at 1440x900 desktop:
- [ ] Nav rail visible on right, doesn't overlap content
- [ ] Section links align vertically centered
- [ ] Email at bottom of rail, rotated vertical text readable
- [ ] Theme toggle at top of rail works
- [ ] Active section highlighting updates while scrolling
- [ ] Case studies: first one has text left/image right, second has image left/text right
- [ ] Placeholder case study is visibly muted (50% opacity)
- [ ] Images have rounded corners
- [ ] Spacing between case studies feels right

Check at 390x844 mobile:
- [ ] No nav rail visible
- [ ] Hamburger icon top-right
- [ ] Menu opens full-screen, links work, close works
- [ ] Scroll lock when menu open
- [ ] Case studies stack: image on top, text below for both
- [ ] No alternation on mobile (all same order)

Check dark mode on both viewports:
- [ ] All nav elements use dark tokens
- [ ] Mobile overlay uses dark bg
- [ ] Case study text/images correct in dark mode

**Step 2: Fix any issues found**

Apply fixes as needed. Common things to check:
- The `lg:pr-20` on body may need adjustment if the rail feels too wide/narrow
- Mobile case study image height may need a `max-h` constraint
- The IntersectionObserver rootMargin may need tuning for active section accuracy

**Step 3: Final build verification**

Run: `bun run build`
Expected: Build succeeds, zero errors.

**Step 4: Commit all fixes**

```bash
git add -A
git commit -m "fix: visual polish from review — spacing, alignment, dark mode"
```

---

## Task Dependency Graph

```
Task 1 (section IDs)
  ↓
Task 2 (SideNav component) ──→ Task 3 (integrate SideNav + delete ThemeToggle)
                                  ↓
Task 4 (CaseStudy revamp) ──→ Task 5 (index.astro integration)
                                  ↓
                              Task 6 (Replicate image)
                                  ↓
                              Task 7 (visual review)
```

Tasks 1, 2, and 4 can run in parallel. Task 3 depends on 1+2. Task 5 depends on 4. Task 6 depends on 5. Task 7 depends on 3+6.
