# Railway Template Composer Checklist — Typebot

Apply these settings in the Railway template composer when generating the template from the project.

---

## 1. Healthcheck Settings

### typebot-builder
- **Healthcheck Path:** `/`
- **Healthcheck Timeout:** `120` seconds (accommodates Next.js startup and database migrations)
- **Variable:** `RAILWAY_HEALTHCHECK_PATH` = `/` with description

### typebot-viewer
- **Healthcheck Path:** `/`
- **Healthcheck Timeout:** `120` seconds
- **Variable:** `RAILWAY_HEALTHCHECK_PATH` = `/` with description

### postgres
- No healthcheck needed (Railway manages this automatically)

### redis
- No healthcheck needed (Railway manages this automatically)

---

## 2. Variable Descriptions (Add to EVERY variable)

### typebot-builder Variables

| Variable | Description | Default / Reference |
|----------|-------------|---------------------|
| `PORT` | The port the Typebot builder UI listens on. | `3000` |
| `NODE_ENV` | Node.js environment setting. | `production` |
| `RAILWAY_HEALTHCHECK_PATH` | Endpoint Railway uses to verify the service is healthy. | `/` |
| `DATABASE_URL` | PostgreSQL connection string for bot data and user accounts. Auto-set from the Postgres service. | `${{Postgres.DATABASE_URL}}` |
| `REDIS_URL` | Redis connection URL for session management and caching. Auto-set from the Redis service. | `${{Redis.REDIS_URL}}` |
| `NEXTAUTH_URL` | Public URL of the builder service for authentication callbacks. Must be set to your Railway public domain. | `https://${{Railway.RAILWAY_PUBLIC_DOMAIN}}` |
| `NEXTAUTH_SECRET` | Secret key for NextAuth session encryption. Auto-generated. | `${{secret(32)}}` |
| `ENCRYPTION_SECRET` | Secret key for encrypting sensitive bot data. Must be a 32-character hex string. Auto-generated. | `${{secret(64, "abcdef0123456789")}}` |
| `NEXT_PUBLIC_VIEWER_URL` | Public URL of the viewer service where published bots are rendered. Set to viewer service domain. | `https://your-viewer-domain.railway.app` |
| `ADMIN_EMAIL` | Email address for the initial admin account during first setup. User-provided. | User input |
| `NODE_OPTIONS` | Node.js runtime options for compatibility. | `--no-node-snapshot` |

### typebot-viewer Variables

| Variable | Description | Default / Reference |
|----------|-------------|---------------------|
| `PORT` | The port the Typebot viewer listens on. | `3000` |
| `NODE_ENV` | Node.js environment setting. | `production` |
| `RAILWAY_HEALTHCHECK_PATH` | Endpoint Railway uses to verify the service is healthy. | `/` |
| `DATABASE_URL` | PostgreSQL connection string (shared with builder). Auto-set from the Postgres service. | `${{Postgres.DATABASE_URL}}` |
| `REDIS_URL` | Redis connection URL (shared with builder). Auto-set from the Redis service. | `${{Redis.REDIS_URL}}` |
| `NEXTAUTH_URL` | Public URL of the builder service (for auth validation). Set to builder service domain. | `https://your-builder-domain.railway.app` |
| `NEXTAUTH_SECRET` | Secret key for NextAuth (must match builder service). Auto-generated. | `${{secret(32)}}` |
| `ENCRYPTION_SECRET` | Encryption key (must match builder service). Must be identical to builder. | `${{secret(64, "abcdef0123456789")}}` |
| `NODE_OPTIONS` | Node.js runtime options for compatibility. | `--no-node-snapshot` |

### Postgres Variables

| Variable | Description | Default Value |
|----------|-------------|---------------|
| `POSTGRES_USER` | Username for the Postgres superuser account. | `postgres` |
| `POSTGRES_PASSWORD` | Password for the Postgres superuser. Auto-generated. | `${{secret(16)}}` |
| `POSTGRES_DB` | Default database name created on startup. | `typebot` |
| `PGDATA` | Directory where PostgreSQL stores its data files inside the container. | `/var/lib/postgresql/data/pgdata` |
| `PGPORT` | Port the Postgres database listens on. | `5432` |
| `DATABASE_URL` | Auto-generated connection string from Railway. | Auto-set |

### Redis Variables

| Variable | Description | Default Value |
|----------|-------------|---------------|
| `REDIS_PASSWORD` | Redis authentication password. Auto-generated. | `${{secret(16)}}` |
| `REDIS_PORT` | Port Redis listens on. | `6379` |
| `REDIS_URL` | Full Redis connection string. Auto-set by Railway. | Auto-set |

---

## 3. Auto-Injected Variables — Default Values

For the **Postgres** service, Railway auto-injects these variables. Set their defaults so users don't get "needs configuration" prompts:

| Variable | Default Value | Mark Optional? |
|----------|---------------|----------------|
| `PGDATA` | `/var/lib/postgresql/data/pgdata` | Yes |
| `PGPORT` | `5432` | Yes |
| `POSTGRES_DB` | `typebot` | Yes |
| `POSTGRES_USER` | `postgres` | Yes |

For the **Redis** service, mark these as optional with defaults:

| Variable | Default Value | Mark Optional? |
|----------|---------------|----------------|
| `REDIS_PASSWORD` | `${{secret(16)}}` | Yes |

---

## 4. Critical Settings to Verify

### ENCRYPTION_SECRET Synchronization
- **Builder and Viewer MUST use the SAME `ENCRYPTION_SECRET`** — If they differ, bots and data will be unreadable.
- In the template composer, verify both services reference the same secret variable or explicitly set the same value.

### NEXTAUTH_SECRET Synchronization
- **Builder and Viewer MUST use the SAME `NEXTAUTH_SECRET`** — If they differ, users cannot authenticate across services.
- Verify both services have this variable with the same value.

### Public URL Configuration
- `NEXTAUTH_URL` on the builder must point to the builder's public Railway domain (e.g., `https://typebot-builder.railway.app`)
- `NEXT_PUBLIC_VIEWER_URL` on the builder must point to the viewer's public Railway domain (e.g., `https://typebot-viewer.railway.app`)
- Set these AFTER generating public domains in Railway to ensure they're correct.

### Port Configuration
- Both builder and viewer listen on port `3000` internally.
- Railway automatically assigns different public ports and domains to each service.

---

## 5. Deployment Checklist

### Before Generating the Template
- [ ] Both builder and viewer services are deployed and healthy
- [ ] Postgres migrations have completed successfully
- [ ] Redis is accessible from both services
- [ ] Public domains have been generated for builder and viewer

### In the Template Composer
- [ ] Set healthcheck path to `/` for both builder and viewer
- [ ] Set healthcheck timeout to `120` seconds
- [ ] Add descriptions to ALL variables (see table above)
- [ ] Verify `ENCRYPTION_SECRET` is identical for both services
- [ ] Verify `NEXTAUTH_SECRET` is identical for both services
- [ ] Set default values for all auto-injected Postgres variables
- [ ] Set default values for all Redis variables
- [ ] Mark optional variables (like `ADMIN_EMAIL`) as "Mark as optional"
- [ ] Verify `NEXTAUTH_URL` and `NEXT_PUBLIC_VIEWER_URL` reference correct domains

### Post-Generation Testing
- [ ] Deploy template from a fresh Railway account to verify one-click setup
- [ ] Verify no "needs configuration" prompts appear for Postgres/Redis variables
- [ ] Confirm builder loads and healthcheck passes on `/`
- [ ] Confirm viewer loads and healthcheck passes on `/`
- [ ] Test authentication by logging in with the admin email
- [ ] Create a simple bot in the builder and verify it's accessible from the viewer

---

## 6. Notes on Optional Configuration

### Optional Add-Ons (Configure After First Deployment)

These features can be added after the initial deployment by setting additional environment variables:

- **Email Configuration** — SMTP variables for password resets and notifications
- **S3/MinIO File Storage** — For uploading files in bot conversations
- **Custom Domain** — Use Railway's domain configuration to set a custom domain

### Environment Variable Prefixes

- `NEXT_PUBLIC_*` — Exposed to the browser (safe to include in frontend code)
- All other variables — Private to the server-side application

