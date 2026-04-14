# claude-test-blog

Static blog built with Astro + Tailwind, deployed on Railway.

## Add a post

1. Create a new `.md` file in `src/content/blog/`
2. Add frontmatter:

```yaml
---
title: "Your post title"
description: "One-line description"
pubDate: 2026-04-14
tags: ["optional", "tags"]
draft: false  # optional, defaults to false
---
```

3. Write the post body in markdown below the frontmatter
4. Commit and push to `main`
5. Railway rebuilds and deploys automatically

## Local dev

```bash
npm install
npm run dev
```

Visit http://localhost:4321

## Build

```bash
npm run build   # outputs to dist/
npm start       # serves dist/ on $PORT (used by Railway)
```
