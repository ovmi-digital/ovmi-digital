# Minio Showcase — Implementation Plan

Add minio as the second case study on ovmi.digital, replacing the "Coming soon" placeholder.

## Context

**What minio is:** A Rust-based CLI orchestrator for Claude Code. It spawns AI agents ("minions") to execute multi-task development plans, with anti-rationalization safeguards that verify agents actually finish their work instead of declaring victory early.

**Key differentiator:** 4-layer completion gate — artifact checks, LLM evaluation, Claude Code hooks, and circuit breakers. No other tool explicitly catches AI agents cutting corners.

**GitHub:** https://github.com/micovi/minio

**Logo:** `/Users/ovee/dev/micovi/minio/logo.png` — a cute yellow/blue minion character (square, cartoon style, blue overalls, yellow head).

**Site pattern:** Case studies are hardcoded `<CaseStudy>` component instances in `/src/pages/index.astro`. Each has a name, tagline, image (WebP in `/public/images/`), optional URL, sections array, and imagePosition.

---

## Steps

### 1. Generate showcase image with Replicate

Use the Replicate MCP server (`mcp__replicate-code-mode__execute`) to generate an abstract artwork featuring the minio logo.

**Requirements:**
- Use an image generation model (flux or similar) via Replicate
- The composition should place the minio minion character (yellow/blue cube mascot) as the centerpiece
- Background should be abstract/stylized — fluid shapes or geometric patterns
- Color palette: blues and yellows (matching the logo) with a light/cream base to match the site's light mode aesthetic
- Landscape orientation, roughly 16:9 or similar wide format
- The style should feel cohesive with the Lola.pet image (organic abstract shapes on a light background) but with minio's own color identity
- Reference the logo at `/Users/ovee/dev/micovi/minio/logo.png` for the character design

**Prompt guidance for the model:**
> Abstract minimalist artwork featuring a cute yellow and blue cube-shaped robot mascot character centered on a cream/off-white background. Soft organic blob shapes in blue and yellow tones around the edges, similar to paper cut-out style. Clean, modern, editorial illustration. No text.

**Output:** Save the generated image, convert to WebP, and place at `/Users/ovee/dev/micovi/ovmi-digital/public/images/minio-abstract.webp`. Keep file size under 50KB if possible (optimize with `cwebp` or similar).

### 2. Write the case study copy

Replace the "Coming soon" placeholder (lines 49-57 in `/src/pages/index.astro`) with a full minio case study.

**Component props:**
```astro
<CaseStudy
  name="Minio"
  tagline="Spawn minions that actually finish the job."
  imageSrc="/images/minio-abstract.webp"
  imageAlt="Abstract artwork featuring the Minio mascot"
  url="https://github.com/micovi/minio"
  imagePosition="left"
  sections={[
    {
      label: "The problem",
      content: "...",
    },
    {
      label: "What we built",
      content: "...",
    },
    {
      label: "How it works",
      content: "...",
    },
  ]}
/>
```

**Copy guidelines for each section:**

**"The problem"** — Lead with the pain point. Claude Code agents frequently rationalize incomplete work. They say "these issues were pre-existing" or "I'll leave this for a follow-up." They declare victory while tasks remain half-done. Anyone running multi-step agent workflows has hit this wall.

**"What we built"** — Describe minio as a Rust CLI that orchestrates Claude Code agents through development plans. Mention the 4-layer gate: artifact verification, LLM-powered rationalization detection, Claude Code hooks, and circuit breakers. Mention it supports custom agent roles (researcher, developer, tester, reviewer) defined as simple markdown files. Keep it specific but concise.

**"How it works"** — Briefly describe the flow: write a plan (JSON with tasks and dependencies), run `minio run`, and it handles agent spawning, task sequencing, completion verification, and git commits. Mention state persistence — you can Ctrl+C and resume where you left off. Mention it's open source on GitHub.

**Voice rules (from CLAUDE.md):**
- Short sentences. No filler.
- No exclamation marks in headings.
- No buzzwords: "cutting-edge," "innovative," "game-changing," "we're passionate about."
- Specific over generic. Show thinking, not just output.
- Confident without being loud.

**Reference the minio README at `/Users/ovee/dev/micovi/minio/README.md` for accurate technical details.**

### 3. Verify

- Run `bun run build` in `/Users/ovee/dev/micovi/ovmi-digital/` to confirm no build errors
- Run `bun run dev` and visually confirm the case study renders correctly (image loads, layout alternates left/right with Lola.pet, dark mode works)
- Check that the image file size is reasonable (<50KB WebP)
- Confirm the GitHub link works

---

## Files to modify

| File | Action |
|------|--------|
| `/src/pages/index.astro` | Replace "Coming soon" CaseStudy (lines 49-57) with minio case study |
| `/public/images/minio-abstract.webp` | New file — generated showcase image |

## Dependencies

- Replicate MCP server for image generation
- `cwebp` or equivalent for WebP conversion (or generate WebP directly)
- minio source at `/Users/ovee/dev/micovi/minio/` for reference (README, logo)
