---
title: "Home"
---

This is a minimal Hugo site to verify that `.cache` is persisted between
Cloudflare Pages builds.

Check the build logs for:
- **First build**: "No build output detected to cache" should NOT appear
  _after_ the build step. The `.cache` directory should be detected and saved.
- **Second build**: The `.cache` directory should be restored, and Hugo should
  re-use cached remote resources instead of re-fetching them.
