# Incident: OAuth access token leaked into the dashboard error banner

Source: commit `f06e810`.

## What happened
An early error path did `JSON.stringify(err)` on a raw axios error from the QBO client and displayed it in the dashboard banner. Axios errors embed the full request config — **including the `Authorization: Bearer <access token>` header**. The live access token was rendered in the browser.

## The fix
`qboError()` in `src/qbo.ts` sanitizes every QBO error to status code + QBO Fault body only, before it can reach a route handler. Nothing else from the error object is ever surfaced or logged.

## Rules
- Never stringify/log a raw HTTP-client error object anywhere in this app.
- New QBO call sites must go through the existing wrapper (`withThrottleAndRetry` + `qboError`), never call the client directly.
- Exposure was brief and tokens rotate on refresh, but treat any similar leak as requiring disconnect/reconnect of the QBO connection (new token pair).
