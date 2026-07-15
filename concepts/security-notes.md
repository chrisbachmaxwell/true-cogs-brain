# Security notes — current posture and obligations

**As of 2026-07-15.**

## Open items (highest first)
1. **The dashboard is publicly reachable.** Auth code exists but only enforces when `RESEND_API_KEY` is set — it isn't. Anyone with the URL sees full financials. Current goal: [../goals/01-auth-gate.md](../goals/01-auth-gate.md).
2. **Railway account token `f406ac63-…` was pasted into a chat session** (2026-07-14) and appears in session transcripts. Chris must rotate it (railway.com → Account → Tokens) once deploy work settles. (verify after: 2026-08)

## Standing design (don't regress)
- QBO OAuth tokens: AES-256-GCM in Postgres, key from `TOKEN_ENCRYPTION_KEY` (Railway env only — never materialize locally).
- Auth: magic links via Resend — single-use 15-min tokens stored SHA-256-hashed; 30-day HMAC cookies with timing-safe compare and allowlist recheck on every request; no user enumeration; rate-limited. Allowlist default `chrism@pictureline.com` (`AUTH_ALLOWED_EMAILS` to extend).
- **No `express.static`** — every page is an explicit route so the auth gate can't be bypassed; only `/theme.css`, `/app.js`, `/login`, `/connect`, `/callback`, `/health` are open. Keep it that way when adding pages.
- `requireAuth` must check `req.originalUrl` (mount-relative `req.path` broke API 401s once).
- Errors sanitized via `qboError` — see [token-leak-error-sanitization.md](token-leak-error-sanitization.md).
- App is read-only toward QuickBooks by design (D2); never add write scopes.
