---
title: "Remote Images Test"
date: 2026-04-13
---

This page uses remote images via Hugo's `resources.GetRemote` to exercise the
build cache. On a cache hit, Hugo skips the HTTP fetch entirely.

{{< remote-image url="https://picsum.photos/id/10/800/600" alt="Remote image 1" >}}
{{< remote-image url="https://picsum.photos/id/20/800/600" alt="Remote image 2" >}}
{{< remote-image url="https://picsum.photos/id/30/800/600" alt="Remote image 3" >}}
{{< remote-image url="https://picsum.photos/id/40/800/600" alt="Remote image 4" >}}
{{< remote-image url="https://picsum.photos/id/50/800/600" alt="Remote image 5" >}}
