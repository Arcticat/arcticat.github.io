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
   tags: ["data", "python"]
   ---

   Post body in Markdown. Images: put the file next to this one and
   reference it as ![alt text](./image.jpg).
   ```

   `tags` is optional (omit the line entirely if you don't want any).
   Each tag automatically gets its own page at `/tags/<tag>/` listing
   every post that carries it.

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