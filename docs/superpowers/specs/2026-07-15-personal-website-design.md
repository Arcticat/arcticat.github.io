# Personal Website Design

**Date:** 2026-07-15
**Status:** Approved pending user review

## Goal

A personal website hosted on GitHub Pages at `https://<username>.github.io`, with a homepage self-introduction and an English-language blog. The owner is new to web development and git; the site must stay easy to maintain: writing a post is adding one Markdown file, and nothing else requires regular attention.

## Hard Constraints

- **No over-engineering.** Maintainability beats features and visual ambition wherever they conflict.
- Dependencies: Astro core only. No UI frameworks (React/Vue), no CSS frameworks (Tailwind), no plugins unless a concrete need appears.
- Styling: plain CSS in a small number of files.
- Content: Markdown files via Astro content collections. No CMS, no database.
- Hosting: GitHub Pages, free tier, default `<username>.github.io` domain (custom domain can be added later without redesign).

## Architecture

- **Framework:** Astro (static output, zero client-side JavaScript by default).
- **Repository:** GitHub repo named `<username>.github.io`.
- **Deployment:** GitHub Actions workflow (Astro's official `withastro/action`) builds and deploys to GitHub Pages on every push to `main`. After one-time setup, publishing is fully automatic.
- **Local preview:** `npm run dev` serves the site at `localhost:4321` with live reload. Requires Node.js (one-time install on the Mac).

## Pages

| Route | Purpose |
|---|---|
| `/` | Self-introduction: name, short English bio (mentions interests/hobbies), contact/social links, plus the 3 most recent blog posts. |
| `/blog` | All posts, newest first: title, date, one-line description. |
| `/blog/<slug>` | Single post page, typography optimized for long-form reading. |
| `404` | Simple not-found page linking home. |

Shared layout: small header with site name + nav (Home, Blog), minimal footer. Two layout files at most (base layout + blog post layout).

## Content Model

Blog posts live in `src/content/blog/` as Markdown files with frontmatter:

```yaml
title: "Post title"
date: 2026-07-15
description: "One-line summary shown in lists."
```

Optional later: `tags`. Images are placed alongside content and referenced relatively. Adding a post = adding one `.md` file; the blog index and homepage update automatically.

## Visual Design

Direction: distinctive but restrained — a personal feel achieved through typography pairing, one accent color, and subtle hover effects. English-first typography. No animation libraries, no heavy effects. Concrete colors/fonts are iterated on the live local preview with the owner rather than specified up front. Design choices must not add dependencies or maintenance burden.

## Publishing Workflow (day-to-day)

1. Write a Markdown file in `src/content/blog/`.
2. Preview locally with `npm run dev` (optional).
3. Publish: commit + push (with Claude Code's help, or one memorized command sequence). The site updates automatically within ~1 minute.

## Error Handling / Failure Modes

- Build failures (e.g., malformed frontmatter) surface in the GitHub Actions tab and block deploy; the live site stays on the last good version — a broken push never takes the site down.
- Astro's content collection schema validates frontmatter locally at dev/build time with clear error messages.

## Verification

- Each implementation step is verified in the local browser preview.
- Final check on the live `https://<username>.github.io` URL after first deploy: all pages render, links work, a test post publishes end-to-end.

## Information Needed From Owner (at implementation time)

- GitHub username.
- Display name and a short self-introduction (Chinese is fine; will be written into English bio).
- Contact/social links to show on the homepage.

## Out of Scope (deliberately)

- Comments, analytics, search, RSS beyond Astro's near-free RSS option, tags/categories, dark mode toggle, custom domain, CMS. All can be added later; none are needed at launch.
