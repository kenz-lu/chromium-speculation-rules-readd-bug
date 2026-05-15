# Bug: Re-adding speculation rules after prerender activation doesn't trigger new prerender

After a prerendered page is activated (user navigates to it), returning to the originating page and dynamically re-injecting the same speculation rules does not trigger a new prerender. The browser appears to deduplicate or suppress the rule even though the previous prerender was consumed.

## Repro

```
node server.js
# open http://localhost:3000
```

1. Open `http://localhost:3000` — check `chrome://speculation-rules-internals` to confirm prerender starts
2. Click **"Open target in new tab"** — target shows **PRERENDER ACTIVATED**
3. Switch back to the original tab
4. On `visibilitychange`, the page re-appends a fresh `<script type="speculationrules">` element
5. Check `chrome://speculation-rules-internals` — **no new prerender is triggered**

## Setup

- `index.html` — Speculation rule prerenders `/target.html` with `target_hint: "_blank"`. On `visibilitychange` (tab regains focus), a fresh speculation rules script is appended to re-trigger prerendering.
- `target.html` — Shows prerender activation status.
- `server.js` — Simple static file server.

## Expected

Re-injecting speculation rules after the previous prerender was activated should start a new prerender, since the old one was consumed.

## Actual

No new prerender is initiated. The browser treats the re-added rule as a duplicate of the already-consumed prerender and does not start a fresh one. Subsequent navigations to the target are not prerendered.

## Workarounds Attempted

- Removing the old `<script>` before appending the new one — no effect
- Adding a delay (`setTimeout`) before appending — no effect
- Using a random `tag` field to differentiate rules — no effect
- Changing the URL with a cache-busting param — works but defeats the purpose
