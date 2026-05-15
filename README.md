# Bug: Re-added speculation rules don't trigger prerender after activation

After a prerender is activated, re-injecting the same speculation rules (even after removing the old script element) does not start a new prerender.

## Repro

```
node server.js
# open http://localhost:3000
```

1. Open page — confirm prerender starts in `chrome://speculation-rules-internals`
2. Click **"Open target in new tab"** — activates prerender
3. Switch back — `visibilitychange` removes old rules and appends fresh ones
4. Check `chrome://speculation-rules-internals` — **no new prerender**

## Files

- `index.html` — Speculation rules + visibilitychange re-injection logic
- `target.html` — Shows prerender activation status
- `server.js` — Static file server

## Expected

Re-injecting rules after the previous prerender was consumed should start a new prerender.

## Actual

Browser treats the re-added rule as a duplicate and does not prerender again.
