---
title: "Hello, world"
description: "The first post on claude-test-blog — proving the markdown-to-deploy pipeline works."
pubDate: 2026-04-14
tags: ["meta", "astro"]
---

Welcome to **claude-test-blog**.

This post exists to verify the entire workflow: write a markdown file, push it to `main`, and Railway rebuilds and serves the static site automatically.

## How it works

1. Drop a `.md` file into `src/content/blog/`
2. Add frontmatter (`title`, `description`, `pubDate`, optional `tags` and `draft`)
3. Commit and push to `main`
4. Railway detects the push, runs `npm run build`, and starts serving the new `dist/`

That's the whole loop.

## What's next

- More posts
- Maybe a tag index page
- RSS feed
