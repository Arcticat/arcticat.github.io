# Personal Website Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build and deploy a personal website (homepage + English blog) to GitHub Pages at https://arcticat.github.io.

**Architecture:** Astro 5 static site in the existing repo `Arcticat/arcticat.github.io`. Blog posts are Markdown files in a content collection. GitHub Actions builds and deploys to GitHub Pages on every push to `main`.

**Tech Stack:** Astro ^5 (only npm dependency), plain CSS, Google Fonts via `<link>` (no npm dep), GitHub Actions (`withastro/action`).

## Global Constraints

- Dependencies: **Astro core only.** No UI frameworks, no CSS frameworks, no Astro integrations/plugins.
- Styling: plain CSS in `src/styles/global.css` plus small scoped `<style>` blocks in components.
- Owner display name: **Chenghan Song**. Site title: **Chenghan Song**.
- Bio copy (verbatim): "Hi, I'm Chenghan Song — a data enthusiast who loves exploring new things. I write here about data, ideas, and whatever I'm currently curious about."
- Contact: email only — `s.chenghan@outlook.com`. No other social links.
- Site URL: `https://arcticat.github.io`. Repo: `Arcticat/arcticat.github.io`, branch `main`.
- Visual direction: light background, ink-navy text, one arctic-blue accent (`#2b7a9b`), Fraunces for headings, system sans for body, subtle hover effects only. No animation libraries.
- Verification is `npm run build` + browser checks (no test framework — deliberate YAGNI; a broken build blocks deploy, and frontmatter is schema-validated at build time).
- Every commit message ends with:
  `Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>`

---

### Task 1: Scaffold Astro project

**Files:**
- Create: `package.json`
- Create: `astro.config.mjs`
- Create: `tsconfig.json`
- Create: `.gitignore`
- Create: `src/pages/index.astro` (placeholder, replaced in Task 4)

**Interfaces:**
- Produces: a working Astro project where `npm run dev` serves the site on `http://localhost:4321` and `npm run build` outputs to `dist/`. `astro.config.mjs` exports config with `site: 'https://arcticat.github.io'`.

- [ ] **Step 1: Write `package.json`**

```json
{
  "name": "arcticat.github.io",
  "type": "module",
  "version": "1.0.0",
  "private": true,
  "scripts": {
    "dev": "astro dev",
    "build": "astro build",
    "preview": "astro preview"
  },
  "dependencies": {
    "astro": "^5.0.0"
  }
}
```

- [ ] **Step 2: Write `astro.config.mjs`**

```js
import { defineConfig } from 'astro/config';

export default defineConfig({
  site: 'https://arcticat.github.io',
});
```

- [ ] **Step 3: Write `tsconfig.json`**

```json
{
  "extends": "astro/tsconfigs/base"
}
```

- [ ] **Step 4: Write `.gitignore`**

```
node_modules/
dist/
.astro/
.DS_Store
```

- [ ] **Step 5: Write placeholder `src/pages/index.astro`**

```astro
---
---

<html lang="en">
  <head>
    <meta charset="utf-8" />
    <title>Chenghan Song</title>
  </head>
  <body>
    <h1>Hello</h1>
  </body>
</html>
```

- [ ] **Step 6: Install dependencies**

Run: `npm install`
Expected: completes without errors; `node_modules/` and `package-lock.json` appear.

- [ ] **Step 7: Verify build works**

Run: `npm run build`
Expected: output ends with a line like `Complete!` and `dist/index.html` exists.

- [ ] **Step 8: Commit**

```bash
git add package.json package-lock.json astro.config.mjs tsconfig.json .gitignore src/pages/index.astro
git commit -m "feat: scaffold Astro project

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
```

---

### Task 2: Blog content collection + sample post

**Files:**
- Create: `src/content.config.ts`
- Create: `src/content/blog/hello-world.md`

**Interfaces:**
- Consumes: Astro project from Task 1.
- Produces: collection `blog` queryable via `getCollection('blog')` from `astro:content`. Each entry has `id: string` (filename without extension, e.g. `hello-world`) and `data: { title: string; date: Date; description: string }`.

- [ ] **Step 1: Write `src/content.config.ts`**

```ts
import { defineCollection, z } from 'astro:content';
import { glob } from 'astro/loaders';

const blog = defineCollection({
  loader: glob({ pattern: '**/*.md', base: './src/content/blog' }),
  schema: z.object({
    title: z.string(),
    date: z.coerce.date(),
    description: z.string(),
  }),
});

export const collections = { blog };
```

- [ ] **Step 2: Write `src/content/blog/hello-world.md`**

```markdown
---
title: "Hello, World"
date: 2026-07-15
description: "First post — why I built this site and what to expect here."
---

Welcome to my corner of the internet.

I built this site as a home for my writing — notes on data, things I'm
learning, and whatever I'm currently curious about. Posts are written in
Markdown and published straight from my laptop.

More soon.
```

- [ ] **Step 3: Verify the schema catches bad frontmatter**

Temporarily remove the `date:` line from `hello-world.md`, run `npm run build`.
Expected: build **fails** with a content collection validation error mentioning `date`.
Restore the `date:` line, run `npm run build` again.
Expected: build succeeds.

- [ ] **Step 4: Commit**

```bash
git add src/content.config.ts src/content/blog/hello-world.md
git commit -m "feat: add blog content collection with sample post

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
```

---

### Task 3: Base layout + global styles

**Files:**
- Create: `src/layouts/BaseLayout.astro`
- Create: `src/styles/global.css`

**Interfaces:**
- Consumes: nothing from earlier tasks (standalone).
- Produces: `BaseLayout` component with props `{ title: string; description?: string }`, rendering `<slot />` inside `<main class="container">`, with site header (nav: Home, Blog) and footer. All pages in Tasks 4–5 wrap themselves in it.

- [ ] **Step 1: Write `src/styles/global.css`**

```css
:root {
  --ink: #1a2332;
  --ink-soft: #55647a;
  --bg: #fbfaf7;
  --accent: #2b7a9b;
  --accent-deep: #1d5a75;
  --line: #e5e1d8;
  --font-display: 'Fraunces', Georgia, serif;
  --font-body: system-ui, -apple-system, 'Segoe UI', sans-serif;
}

* {
  box-sizing: border-box;
}

body {
  margin: 0;
  background: var(--bg);
  color: var(--ink);
  font-family: var(--font-body);
  font-size: 1.0625rem;
  line-height: 1.7;
}

h1, h2, h3 {
  font-family: var(--font-display);
  line-height: 1.2;
  margin: 0 0 0.5em;
}

a {
  color: var(--accent);
  text-decoration-thickness: 1px;
  text-underline-offset: 3px;
  transition: color 0.15s ease;
}

a:hover {
  color: var(--accent-deep);
}

.container {
  max-width: 42rem;
  margin: 0 auto;
  padding: 0 1.25rem;
}

.site-header {
  border-bottom: 1px solid var(--line);
}

.site-header .container {
  display: flex;
  align-items: baseline;
  justify-content: space-between;
  padding-top: 1.5rem;
  padding-bottom: 1.5rem;
}

.site-name {
  font-family: var(--font-display);
  font-weight: 600;
  font-size: 1.15rem;
  color: var(--ink);
  text-decoration: none;
}

.site-name:hover {
  color: var(--accent);
}

.site-nav a {
  margin-left: 1.5rem;
  color: var(--ink-soft);
  text-decoration: none;
}

.site-nav a:hover {
  color: var(--accent);
}

main.container {
  padding-top: 3rem;
  padding-bottom: 4rem;
  min-height: 60vh;
}

.site-footer {
  border-top: 1px solid var(--line);
  color: var(--ink-soft);
  font-size: 0.9rem;
}

.site-footer .container {
  padding-top: 1.5rem;
  padding-bottom: 2rem;
}
```

- [ ] **Step 2: Write `src/layouts/BaseLayout.astro`**

```astro
---
import '../styles/global.css';

interface Props {
  title: string;
  description?: string;
}

const { title, description = "Chenghan Song's personal site and blog." } = Astro.props;
---

<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <meta name="description" content={description} />
    <link rel="preconnect" href="https://fonts.googleapis.com" />
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
    <link
      href="https://fonts.googleapis.com/css2?family=Fraunces:opsz,wght@9..144,500;9..144,600;9..144,700&display=swap"
      rel="stylesheet"
    />
    <title>{title}</title>
  </head>
  <body>
    <header class="site-header">
      <div class="container">
        <a href="/" class="site-name">Chenghan Song</a>
        <nav class="site-nav">
          <a href="/">Home</a>
          <a href="/blog">Blog</a>
        </nav>
      </div>
    </header>
    <main class="container">
      <slot />
    </main>
    <footer class="site-footer">
      <div class="container">
        © 2026 Chenghan Song · <a href="mailto:s.chenghan@outlook.com">Email</a>
      </div>
    </footer>
  </body>
</html>
```

- [ ] **Step 3: Verify build**

Run: `npm run build`
Expected: succeeds (layout not yet used by any page — this just catches syntax errors).

- [ ] **Step 4: Commit**

```bash
git add src/layouts/BaseLayout.astro src/styles/global.css
git commit -m "feat: add base layout and global styles

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
```

---

### Task 4: Homepage

**Files:**
- Modify: `src/pages/index.astro` (replace placeholder entirely)

**Interfaces:**
- Consumes: `BaseLayout` (`{ title, description? }`) from Task 3; `getCollection('blog')` entries (`{ id, data: { title, date, description } }`) from Task 2.
- Produces: homepage at `/` with hero (name, bio, email link) and the 3 newest posts linking to `/blog/<id>/`.

- [ ] **Step 1: Replace `src/pages/index.astro`**

```astro
---
import { getCollection } from 'astro:content';
import BaseLayout from '../layouts/BaseLayout.astro';

const posts = (await getCollection('blog'))
  .sort((a, b) => b.data.date.valueOf() - a.data.date.valueOf())
  .slice(0, 3);
---

<BaseLayout title="Chenghan Song">
  <section class="hero">
    <h1>Hi, I'm Chenghan Song<span class="accent-dot">.</span></h1>
    <p class="bio">
      A data enthusiast who loves exploring new things. I write here about
      data, ideas, and whatever I'm currently curious about.
    </p>
    <p>
      <a class="email-link" href="mailto:s.chenghan@outlook.com"
        >s.chenghan@outlook.com</a
      >
    </p>
  </section>

  <section class="recent">
    <h2>Recent writing</h2>
    <ul class="post-list">
      {
        posts.map((post) => (
          <li>
            <a href={`/blog/${post.id}/`}>{post.data.title}</a>
            <time datetime={post.data.date.toISOString()}>
              {post.data.date.toLocaleDateString('en-US', {
                year: 'numeric',
                month: 'short',
                day: 'numeric',
              })}
            </time>
            <p>{post.data.description}</p>
          </li>
        ))
      }
    </ul>
    <a href="/blog">All posts →</a>
  </section>
</BaseLayout>

<style>
  .hero {
    padding: 2rem 0 3rem;
  }

  .hero h1 {
    font-size: 2.75rem;
  }

  .accent-dot {
    color: var(--accent);
  }

  .bio {
    font-size: 1.2rem;
    color: var(--ink-soft);
    max-width: 36rem;
  }

  .email-link {
    font-size: 0.95rem;
  }

  .recent h2 {
    font-size: 1.5rem;
    border-bottom: 1px solid var(--line);
    padding-bottom: 0.5rem;
  }

  .post-list {
    list-style: none;
    padding: 0;
    margin: 0 0 1.5rem;
  }

  .post-list li {
    padding: 1rem 0;
  }

  .post-list li a {
    font-family: var(--font-display);
    font-size: 1.2rem;
    font-weight: 600;
    text-decoration: none;
  }

  .post-list li a:hover {
    text-decoration: underline;
  }

  .post-list time {
    display: block;
    font-size: 0.85rem;
    color: var(--ink-soft);
    margin-top: 0.15rem;
  }

  .post-list p {
    margin: 0.35rem 0 0;
    color: var(--ink-soft);
  }
</style>
```

- [ ] **Step 2: Verify in browser**

Run: `npm run dev` (leave running), open `http://localhost:4321`.
Expected: header with name + nav, hero with name/bio/email, "Recent writing" listing "Hello, World" linking to `/blog/hello-world/` (link 404s for now — that page comes in Task 5).

- [ ] **Step 3: Commit**

```bash
git add src/pages/index.astro
git commit -m "feat: add homepage with bio and recent posts

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
```

---

### Task 5: Blog index, post page, 404

**Files:**
- Create: `src/pages/blog/index.astro`
- Create: `src/pages/blog/[id].astro`
- Create: `src/pages/404.astro`

**Interfaces:**
- Consumes: `BaseLayout` from Task 3; blog collection from Task 2; `render` from `astro:content`.
- Produces: `/blog/` (all posts, newest first), `/blog/<id>/` (rendered post), `/404.html`.

- [ ] **Step 1: Write `src/pages/blog/index.astro`**

```astro
---
import { getCollection } from 'astro:content';
import BaseLayout from '../../layouts/BaseLayout.astro';

const posts = (await getCollection('blog')).sort(
  (a, b) => b.data.date.valueOf() - a.data.date.valueOf()
);
---

<BaseLayout title="Blog · Chenghan Song">
  <h1>Blog</h1>
  <ul class="post-list">
    {
      posts.map((post) => (
        <li>
          <a href={`/blog/${post.id}/`}>{post.data.title}</a>
          <time datetime={post.data.date.toISOString()}>
            {post.data.date.toLocaleDateString('en-US', {
              year: 'numeric',
              month: 'short',
              day: 'numeric',
            })}
          </time>
          <p>{post.data.description}</p>
        </li>
      ))
    }
  </ul>
</BaseLayout>

<style>
  h1 {
    font-size: 2.25rem;
  }

  .post-list {
    list-style: none;
    padding: 0;
    margin: 1.5rem 0 0;
  }

  .post-list li {
    padding: 1.1rem 0;
    border-bottom: 1px solid var(--line);
  }

  .post-list li a {
    font-family: var(--font-display);
    font-size: 1.3rem;
    font-weight: 600;
    text-decoration: none;
  }

  .post-list li a:hover {
    text-decoration: underline;
  }

  .post-list time {
    display: block;
    font-size: 0.85rem;
    color: var(--ink-soft);
    margin-top: 0.15rem;
  }

  .post-list p {
    margin: 0.35rem 0 0;
    color: var(--ink-soft);
  }
</style>
```

- [ ] **Step 2: Write `src/pages/blog/[id].astro`**

```astro
---
import { getCollection, render } from 'astro:content';
import BaseLayout from '../../layouts/BaseLayout.astro';

export async function getStaticPaths() {
  const posts = await getCollection('blog');
  return posts.map((post) => ({
    params: { id: post.id },
    props: { post },
  }));
}

const { post } = Astro.props;
const { Content } = await render(post);
---

<BaseLayout title={`${post.data.title} · Chenghan Song`} description={post.data.description}>
  <article>
    <header class="post-header">
      <h1>{post.data.title}</h1>
      <time datetime={post.data.date.toISOString()}>
        {
          post.data.date.toLocaleDateString('en-US', {
            year: 'numeric',
            month: 'long',
            day: 'numeric',
          })
        }
      </time>
    </header>
    <div class="post-body">
      <Content />
    </div>
  </article>
</BaseLayout>

<style>
  .post-header h1 {
    font-size: 2.25rem;
    margin-bottom: 0.25rem;
  }

  .post-header time {
    color: var(--ink-soft);
    font-size: 0.9rem;
  }

  .post-body {
    margin-top: 2rem;
  }

  .post-body :global(img) {
    max-width: 100%;
    border-radius: 6px;
  }

  .post-body :global(pre) {
    padding: 1rem;
    border-radius: 6px;
    overflow-x: auto;
    font-size: 0.9rem;
  }

  .post-body :global(blockquote) {
    margin: 1.5rem 0;
    padding-left: 1rem;
    border-left: 3px solid var(--accent);
    color: var(--ink-soft);
  }
</style>
```

- [ ] **Step 3: Write `src/pages/404.astro`**

```astro
---
import BaseLayout from '../layouts/BaseLayout.astro';
---

<BaseLayout title="Not found · Chenghan Song">
  <h1>Page not found</h1>
  <p>That page doesn't exist. <a href="/">Head back home</a>.</p>
</BaseLayout>
```

- [ ] **Step 4: Verify in browser**

With `npm run dev` running, check:
- `http://localhost:4321/blog` — lists "Hello, World".
- `http://localhost:4321/blog/hello-world/` — full post renders with title, date, body.
- `http://localhost:4321/nope` — 404 page renders with the layout.
- Homepage "Recent writing" link now works.

- [ ] **Step 5: Verify production build**

Run: `npm run build`
Expected: succeeds; `dist/blog/hello-world/index.html` and `dist/404.html` exist.

- [ ] **Step 6: Commit**

```bash
git add src/pages/blog/index.astro "src/pages/blog/[id].astro" src/pages/404.astro
git commit -m "feat: add blog index, post page, and 404

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
```

---

### Task 6: Deploy to GitHub Pages

**Files:**
- Create: `.github/workflows/deploy.yml`

**Interfaces:**
- Consumes: complete site from Tasks 1–5; repo `Arcticat/arcticat.github.io` with `origin` remote already configured.
- Produces: live site at `https://arcticat.github.io`, auto-redeployed on every push to `main`.

- [ ] **Step 1: Write `.github/workflows/deploy.yml`**

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Build with Astro
        uses: withastro/action@v3

  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

- [ ] **Step 2: Enable GitHub Pages with Actions as the source**

Run: `gh api repos/Arcticat/arcticat.github.io/pages -X POST -f build_type=workflow`
Expected: JSON response with `"build_type": "workflow"`.
If it errors with "already exists", run instead:
`gh api repos/Arcticat/arcticat.github.io/pages -X PUT -f build_type=workflow`

- [ ] **Step 3: Commit and push**

```bash
git add .github/workflows/deploy.yml
git commit -m "feat: add GitHub Pages deploy workflow

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
git push
```

- [ ] **Step 4: Watch the deploy**

Run: `gh run watch --repo Arcticat/arcticat.github.io --exit-status`
Expected: workflow "Deploy to GitHub Pages" completes with success.

- [ ] **Step 5: Verify the live site**

Run: `curl -s -o /dev/null -w "%{http_code}" https://arcticat.github.io/` → expect `200`.
Run: `curl -s -o /dev/null -w "%{http_code}" https://arcticat.github.io/blog/hello-world/` → expect `200`.
Then open `https://arcticat.github.io` in the browser and click through Home → Blog → post.

---

### Task 7: Owner's how-to-publish guide (README)

**Files:**
- Modify: `README.md` (replace the one-line placeholder entirely)

**Interfaces:**
- Consumes: publishing workflow established in Task 6.
- Produces: README that lets the owner (or a future Claude session) publish a post without re-deriving anything.

- [ ] **Step 1: Replace `README.md`**

````markdown
# arcticat.github.io

Personal website of Chenghan Song — homepage + blog. Built with
[Astro](https://astro.build), deployed automatically to GitHub Pages.

Live at **https://arcticat.github.io**

## Write a new post

1. Create a new file in `src/content/blog/`, e.g. `my-post.md`.
   The filename becomes the URL: `my-post.md` → `/blog/my-post/`.
2. Start the file with frontmatter, then write Markdown:

   ```markdown
   ---
   title: "My Post Title"
   date: 2026-08-01
   description: "One-line summary shown in post lists."
   ---

   Post body in Markdown. Images: put the file next to this one and
   reference it as ![alt text](./image.jpg).
   ```

3. Preview locally (optional): `npm run dev` → http://localhost:4321
4. Publish:

   ```bash
   git add .
   git commit -m "post: my post title"
   git push
   ```

   The site rebuilds automatically; changes are live in ~1 minute.
   If the site doesn't update, check the Actions tab on GitHub —
   a red ✗ means the build failed (usually malformed frontmatter)
   and the live site simply stays on the previous version.

## Commands

| Command           | What it does                             |
| ----------------- | ---------------------------------------- |
| `npm run dev`     | Local preview with live reload           |
| `npm run build`   | Production build into `dist/`            |
| `npm run preview` | Serve the production build locally       |
````

- [ ] **Step 2: Commit and push**

```bash
git add README.md
git commit -m "docs: add how-to-publish guide

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
git push
```

- [ ] **Step 3: Final check**

Confirm `https://arcticat.github.io` still returns 200 after the push-triggered redeploy (`gh run watch --repo Arcticat/arcticat.github.io --exit-status`).
