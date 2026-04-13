# banda-1700-hugo-cache-repro

Minimal Hugo site to verify that **Cloudflare Pages** correctly persists the
`.cache` directory between builds.

## Context

- **Jira**: [BANDA-1700](https://jira.cfdata.org/browse/BANDA-1700) - Add Hugo build caching support for Pages and Workers
- **GitHub issue**: [cloudflare/workers-sdk#12969](https://github.com/cloudflare/workers-sdk/issues/12969) - Pages v3: Build cache does not detect `./cache` as it checks after clone, not the build

### The bug

Pages v3 checks for `.cache` **after cloning** but **before running the build
command**. Hugo (and other tools) only _create_ `.cache` during the build, so
the cache is never detected or saved.

### What this repo does

1. Configures Hugo to store all caches under `.cache/` via `hugo.yaml`:

   ```yaml
   caches:
     getresource:
       dir: ":cacheDir/getresource"
       maxAge: -1
   ```

2. Uses `resources.GetRemote` to fetch 5 remote images during the build. These
   HTTP responses are stored in `.cache/getresource/`.

3. The **build command** is:

   ```
   hugo --minify --cacheDir=$PWD/.cache
   ```

## How to verify the fix

### Setup

1. Connect this repo to a Cloudflare Pages project.
2. Set the build command to: `hugo --minify --cacheDir=$PWD/.cache`
3. Set the build output directory to: `public`
4. Enable **Build Caching** in the Pages project settings.

### First build

- Hugo fetches all 5 remote images over the network.
- After the build, check the build log for:
  ```
  Build output cached.
  ```
  (This confirms `.cache` was detected and saved _after_ the build.)

### Second build (trigger a redeploy or push a commit)

- Check the build log for:
  ```
  Restoring build output from cache.
  ```
- Hugo should re-use the cached images from `.cache/getresource/` instead of
  re-fetching them, resulting in a **noticeably faster build**.

### What a broken build looks like

If the fix is **not** in place, you will see:
```
No build output detected to cache. Skipping.
```
This appears _before_ the build runs, meaning the system checked for `.cache`
too early (after clone, before build).
