# The stale-response bug: late responses overwriting the DOM

Source: Chris's screenshot 2026-07-15 (URL said end=2026-06-30, numbers were Jan-1→Jul-15); commit `7ddca5d`.

## The bug
Report pages fire a fetch on every range change. A SLOW request for range A (uncached YTD default) could resolve AFTER a fast cached request for range B and overwrite the DOM — so the page displayed range-A numbers under range-B's URL. Diagnosed by matching the wrong on-screen purchases delta exactly to July's figure ($962,655.84).

## The fix (pattern — reuse everywhere)
`staleGuard()` in `public/app.js`:

```js
let __loadSeq = 0;
function staleGuard() {
  const my = ++__loadSeq;
  return () => my === __loadSeq;
}
```

Every page's `load()` starts with `const fresh = staleGuard();` and checks `if (!fresh()) return;` after each `await` before touching the DOM. Applied to all five pages (index, pnl, bank, cashflow, inventory).

## Rule
Any new page or any new `await` added inside a `load()` must re-check `fresh()` before rendering. Symptom to recognize: "the numbers don't match the date range shown" with no server-side error.
