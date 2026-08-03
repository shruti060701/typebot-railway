# Railway Template Composer Checklist — Typebot

Apply these settings in the Railway template composer when generating the template from the project. Rewritten 2026-07-24 after a full rebuild — the previous version of this file predated the rebuild and was stale (wrong service names, missing MinIO/Valkey entirely, claimed a `REDIS_URL` variable that doesn't actually exist in Typebot's config). Every value below is pulled from the real, currently-running services via `railway variables`, not assumed.

**Real service names in this project (composer will show these, not generic labels):** `typebot-railway` (Builder), `fearless-balance` (Viewer), `minio`, `minio-bucket-creator`, `Postgres`, `valkey`.

---

## 1. Healthcheck Settings

Confirmed via live `curl` against each running service on 2026-07-24, not guessed:

| Service | Healthcheck Path | Confirmed Response |
|---|---|---|
| `typebot-railway` | `/` | `307` (redirects to `/signin` — healthy) |
| `fearless-balance` | `/` | `200` |
| `minio` | `/minio/health/live` | `200` — MinIO's own dedicated healthcheck endpoint, not the bucket path |
| `minio-bucket-creator` | None — one-time job, not a long-running service | — |
| `Postgres` | None needed (Railway manages this) | — |
| `valkey` | None needed (Railway manages this) | — |

**Timeout:** `120` seconds is sufficient for both `typebot-railway` and `fearless-balance` (they report "Ready" within a few hundred ms; the timeout headroom is for cold Docker pulls, not app startup).

**Important history worth knowing if either app service's healthcheck ever mysteriously fails again:** for most of this build, `fearless-balance` legitimately could not pass an HTTP healthcheck at any guessable path (it 404s at `/` without a published bot, by Typebot's own design) — the fix that actually got both services stable was removing `healthcheckPath` from `railway.toml` entirely so Railway fell back to its default TCP-port-open check. The paths above are now confirmed working again as of the final rebuild, but if a fresh deploy from this template ever regresses, falling back to no explicit path (TCP-only) is a known-good escape hatch, not a hack — MinIO in this same project has run healthy with no `healthcheckPath` the entire time.

---

## 2. Variable Descriptions (Add to EVERY variable)

### `typebot-railway` (Builder) Variables

| Variable | Value (currently set) | Mark Optional? | Description |
|----------|------------------------|-----------------|-------------|
| `DATABASE_URL` | `${{Postgres.DATABASE_URL}}` | No | PostgreSQL connection string for bot data and user accounts. |
| `ENCRYPTION_SECRET` | `${{secret(64, "abcdef0123456789")}}` | No | Encrypts sensitive bot data. **Must be identical to the same variable on `fearless-balance`** — if they differ, bots and data become unreadable. |
| `NEXTAUTH_SECRET` | `${{secret(32)}}` | No | Session encryption key for NextAuth. |
| `NEXTAUTH_URL` | `https://${{RAILWAY_PUBLIC_DOMAIN}}` | No | Public URL of this service, used for auth callbacks. |
| `NEXT_PUBLIC_VIEWER_URL` | Set to `fearless-balance`'s public domain (e.g. `https://${{fearless-balance.RAILWAY_PUBLIC_DOMAIN}}`) | No | Public URL of the Viewer service, where published bots are rendered. |
| `HOSTNAME` | `0.0.0.0` | No | **Required, not optional** — without this, the app binds to loopback only and Railway's healthcheck can never reach it, even though the app logs "Ready." Found the hard way this session. |
| `PORT` | `3000` | No | Must be pinned explicitly — Railway's own auto-injected `PORT` (commonly 8080) silently overrides the Docker image's internal default, causing a port mismatch if not set here. |
| `GITHUB_CLIENT_ID` | Your GitHub OAuth App's Client ID | **Yes**, but only if using GitHub login | Required to log in — Typebot has no default login method. Create a free OAuth App at github.com/settings/developers. **You won't have your Railway domain yet at this point** — use a placeholder like `https://example.com` for the Homepage URL and `https://example.com/api/auth/callback/github` for the callback URL, then come back and update both to your real domain once this deploys. |
| `GITHUB_CLIENT_SECRET` | Your GitHub OAuth App's Client Secret | **Yes**, but only if using GitHub login | Pairs with `GITHUB_CLIENT_ID` from the same OAuth App — GitHub only shows this once, copy it right after creating the app. This project uses GitHub OAuth instead of SMTP/email auth since Gmail is confirmed not to work on Railway (per the official Typebot Railway template's own notes) and no other SMTP provider was configured. |

**Corrected 2026-07-24 — the original version of this description said "Homepage URL = your domain" with no mention that you don't have a domain yet at this point in the deploy flow.** Shruti hit this exact confusion live while testing the published template, which is what caught it. This is also the text that should be pasted into the actual composer field's Description box, not just documented here — that field is one of the only two places (along with `TEMPLATE_DESCRIPTION.md`) a real deployer is likely to actually see, since `README.md` lives in the GitHub repo and most people deploying from the Railway marketplace never click through to it.
| `S3_ACCESS_KEY` | `${{minio.MINIO_ROOT_USER}}` | No | MinIO access key for media uploads. Live reference, not a copied value — MinIO's credentials can be regenerated independently. |
| `S3_SECRET_KEY` | `${{minio.MINIO_ROOT_PASSWORD}}` | No | MinIO secret key. |
| `S3_BUCKET` | `typebot` | No | Bucket name created by `minio-bucket-creator`. |
| `S3_ENDPOINT` | `minio`'s public domain (e.g. `${{minio.RAILWAY_PUBLIC_DOMAIN}}`) | No | Must be MinIO's **public** domain, not the private one — uploaded media needs to be servable back to end users chatting with the bot, not just reachable internally. |
| `S3_SSL` | `true` | No | Railway domains are HTTPS-only. |

### `fearless-balance` (Viewer) Variables

| Variable | Value (currently set) | Mark Optional? | Description |
|----------|------------------------|-----------------|-------------|
| `DATABASE_URL` | `${{Postgres.DATABASE_URL}}` | No | Same database as the Builder. |
| `ENCRYPTION_SECRET` | Same value as `typebot-railway`'s | No | **Must match the Builder's `ENCRYPTION_SECRET` exactly.** |
| `NEXTAUTH_URL` | Set to this service's own public domain | No | Confirmed the Viewer does NOT need `NEXTAUTH_SECRET` set at all — it currently has none and is genuinely healthy. The previous version of this checklist claimed Builder/Viewer `NEXTAUTH_SECRET` must be synced; that was never actually verified and appears to be wrong for this app's real architecture. |
| `NEXT_PUBLIC_VIEWER_URL` | This service's own public domain | No | Self-referential — the Viewer needs to know its own public URL. |
| `HOSTNAME` | `0.0.0.0` | No | Same reasoning as Builder — required for the healthcheck to reach it at all. |
| `PORT` | `3000` | No | Same reasoning as Builder. |
| `S3_ACCESS_KEY` / `S3_SECRET_KEY` / `S3_BUCKET` / `S3_ENDPOINT` / `S3_SSL` | Same as Builder's | No | Same MinIO wiring, so the Viewer can serve uploaded media to end users during a chat. |

### `minio` Variables

| Variable | Value (currently set) | Mark Optional? | Description |
|----------|------------------------|-----------------|-------------|
| `MINIO_ROOT_USER` | `minioadmin` | No | MinIO admin username. |
| `MINIO_ROOT_PASSWORD` | `${{secret(24)}}` | No | Password for the MinIO admin account. Auto-generated fresh for each deployment. **Correction to an earlier version of this note:** during our own debugging, manually re-copying this value via CLI across several redeploys caused drift between what MinIO had and what other services referenced — that was an artifact of our manual process, not a real limitation of `${{secret()}}` itself. For the actual published template, `${{secret()}}` is correct and safer than a fixed literal — a fixed value would mean every single deployer of this template shares the exact same MinIO admin password, letting anyone log into anyone else's MinIO console. |
| `PORT` | `9000` | No | **Required** — image-based services (no Dockerfile) have no way for Railway to know which port to route a public domain to without this being set explicitly. Without it, the public domain returns a `502` with an `x-railway-fallback: true` header, which looks like a generic platform error rather than an obvious missing-config problem. |

**Custom Start Command (Settings → Deploy, not a variable):**
```
minio server /data --console-address ":9001"
```
Must include the `minio` binary name itself — Railway's Custom Start Command field does not implicitly prepend the image's entrypoint. Omitting it produces `The executable 'server' could not be found.`

**No separate "Console" service.** The official reference template (`railway.com/deploy/typebot`) uses a separate MinIO Console service, but that's built from `railwayapp-templates/minio-console`'s source (`go install console@latest`) with no publishable Docker image — deploying it would require a GitHub-repo connection, which is a confirmed-broken CLI operation in this whole project (`railway add --repo` doesn't work). Consolidated instead into MinIO's own built-in console via `--console-address ":9001"` above — same admin UI functionality, one service instead of two.

### `minio-bucket-creator` Variables

| Variable | Value (currently set) | Mark Optional? | Description |
|----------|------------------------|-----------------|-------------|
| `MINIO_ROOT_USER` | `${{minio.MINIO_ROOT_USER}}` | No | Live reference to MinIO's credentials, not a copy. |
| `MINIO_ROOT_PASSWORD` | `${{minio.MINIO_ROOT_PASSWORD}}` | No | Same. |

**Custom Start Command (Settings → Deploy):**
```
sh -c 'mc alias set myminio http://minio.railway.internal:9000 "$MINIO_ROOT_USER" "$MINIO_ROOT_PASSWORD" && mc mb myminio/typebot --ignore-existing && mc anonymous set download myminio/typebot'
```
**Must be wrapped in `sh -c '...'`** — Railway's Custom Start Command field does not appear to run through a shell by default, so bare `$VARIABLE` references get passed to `mc` as literal unexpanded text (producing the exact same "Access Key Id does not exist" error as a real credential mismatch, which cost significant debugging time before this was identified as the actual cause).

**This is a one-time job, not a long-running service.** Its whole purpose is creating the public `typebot` bucket in MinIO. Confirmed via its own logs that it already succeeded (`Bucket created successfully myminio/typebot`, `Access permission ... set to download`) — per the official reference template's own notes, it's safe to delete once its logs confirm success. Not required to keep running for the template to function.

### `Postgres` Variables (managed plugin — `railwayapp-templates/postgres-ssl`)

| Variable | Value | Mark Optional? | Description |
|----------|-------|-----------------|-------------|
| `DATABASE_URL` | Auto-set (private connection string) | No | Primary connection string, used by both Builder and Viewer. |
| `DATABASE_PUBLIC_URL` | Auto-set (public connection string) | No | For external access outside Railway. |
| `PGDATA` | `/var/lib/postgresql/data/pgdata` | **Yes** | Directory where Postgres stores its data files. |
| `PGHOST` | Auto-set: `${{RAILWAY_PRIVATE_DOMAIN}}` | No | Internal hostname. |
| `PGPORT` | `5432` | **Yes** | Port Postgres listens on. |
| `PGUSER` | Auto-set: `${{POSTGRES_USER}}` | No | Username reference. |
| `PGPASSWORD` | Auto-set: `${{POSTGRES_PASSWORD}}` | No | Password reference. |
| `PGDATABASE` | Auto-set: `${{POSTGRES_DB}}` | No | Database name reference. |
| `POSTGRES_USER` | `postgres` | **Yes** | Superuser username. |
| `POSTGRES_PASSWORD` | Already prefilled by Railway: `${{secret(32, "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ")}}` — leave as is | No | Auto-generated superuser password. **Corrected 2026-07-24** — this file previously said `${{secret(16)}}`, which was a guess, not what Railway's managed Postgres plugin actually prefills. Same class of mistake caught once already on the Evolution API template's checklist; this is now confirmed via screenshot to be Railway's standard default for this plugin across multiple templates, not project-specific. |
| `POSTGRES_DB` | `railway` | **Yes** | Default database name (Railway's own default, not `typebot` — the app doesn't care what it's named). |
| `SSL_CERT_DAYS` | `820` | **Yes** | SSL certificate validity period. |

### `valkey` Variables

**No custom variables are set, and Valkey is currently not wired to any application.** Confirmed by checking Typebot's own `.env.example` directly — it has zero Redis/Valkey-related environment variables. It's included in this project purely for architectural parity with the official reference template (which also includes it, seemingly for future/optional caching), not because the app currently requires it. If the composer flags this service for missing configuration, there's genuinely nothing to configure — it runs with its image defaults.

---

## 3. Auto-Injected Variables — Default Values

Same six Postgres variables this project always flags (see Postgres table above for the actual values): `PGDATA`, `PGPORT`, `POSTGRES_DB`, `POSTGRES_USER`, `SSL_CERT_DAYS`, and (if shown) `RAILWAY_DEPLOYMENT_DRAINING_SECONDS` — mark all six as optional with the defaults listed above.

---

## 4. Critical Settings to Verify Before Publishing

- **`ENCRYPTION_SECRET` must be identical on `typebot-railway` and `fearless-balance`** — confirmed real requirement, differing values break bot/data readability.
- **`HOSTNAME=0.0.0.0` and `PORT=3000` must be set on both app services** — without these, healthchecks fail even though the app logs "Ready."
- **`minio` must have `PORT=9000`** — without it, its public domain returns 502 regardless of whether the server itself is healthy.
- **`S3_ENDPOINT` must be MinIO's public domain**, not its private/internal one — media needs to be servable to end users outside Railway's network, not just reachable by the app services.
- Use `${{secret(24)}}` for `minio`'s `MINIO_ROOT_PASSWORD` in the published template (see MinIO section above — corrected from an earlier, wrong version of this note).

---

## 5. Post-Generation Testing

1. Deploy the template fresh (incognito/different account) and confirm no "needs configuration" prompts for Postgres's six flagged variables.
2. Confirm `typebot-railway` root returns `307 → /signin`, not a crash.
3. Confirm `fearless-balance` root returns `200`.
4. Confirm `minio`'s `/minio/health/live` returns `200`.
5. If GitHub OAuth variables are provided, test "Continue with GitHub" actually completes a login.
6. Create a bot in the Builder, publish it, and verify it renders correctly at the Viewer's URL — this is the one thing that hasn't been end-to-end tested yet as of 2026-07-24 (deployment health and login were verified; the actual bot-creation-to-published-bot flow was not).
