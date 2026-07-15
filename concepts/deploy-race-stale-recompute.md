# Dead end: probing deploys with refresh loops

Source: multiple painful incidents, 2026-07-13/14.

## What we tried and why it failed
Railway deploys take 3–5 minutes. To know when new code was live, scripts polled the app — and every approach backfired:

1. **Polling `?refresh=1` on a real month** → each probe STARTED a fresh expensive recompute; probes stacked dozens of concurrent June recomputes and hammered the QBO API.
2. **Polling without refresh** → one probe hit during a partial state and **cached a stale empty-month response**, which then defeated later probes (they read the poisoned cache and concluded the deploy was still old).
3. **Probing for new behavior before the deploy finished** → scripts concluded "bug still present" against old code and triggered pointless re-fixes.

## What actually works
- Ask Railway, not the app: check deploy status via the CLI/build logs, or probe a **static asset** for a marker (`curl .../app.js | grep staleGuard`) — static files change atomically with the deploy and trigger no computation.
- If a cache got poisoned, recompute through the app's own `?refresh=1` path once (a TRUNCATE of `monthly_cache` will be denied by the permission classifier anyway — see permission-classifier-lessons).
- Server-side fix that came out of this: **in-flight dedupe** for range endpoints, so concurrent identical requests share one computation.

## Rule
Never point a polling loop at an endpoint that computes or caches. Probe static assets or the platform's deploy status.
