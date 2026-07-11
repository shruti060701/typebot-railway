# Typebot on Railway

Typebot — open-source visual chatbot builder. Deploy on Railway with one click.

[![Deploy on Railway](https://railway.app/button.svg)](https://railway.app/new/template/TYPEBOT_TEMPLATE_CODE)

## Features

- **Visual chatbot builder** — No-code interface to design and manage conversational flows.
- **Database included** — PostgreSQL and Redis provisioned automatically.
- **Multi-channel deployment** — Embed bots on websites, WhatsApp, Telegram, and more.
- **Built-in analytics** — Track conversation metrics, user interactions, and response rates.
- **Self-hosted control** — Complete data ownership with zero vendor lock-in.
- **Flow builder** — Conditional logic, variable storage, API integrations, and webhooks.

## How to use

1. Click the **Deploy on Railway** button above.
2. Fill in the required environment variables during the template wizard:
   - `ENCRYPTION_SECRET` — Auto-generated 32-character encryption key.
   - `NEXTAUTH_URL` — Auto-set to your Railway builder domain (e.g., `https://your-railway-domain.railway.app`).
   - `NEXT_PUBLIC_VIEWER_URL` — Auto-set to your Railway viewer domain (e.g., `https://your-viewer-domain.railway.app`).
   - `ADMIN_EMAIL` — Email for admin account setup.
3. Wait for deployment to finish (Postgres migrations may take 1-2 minutes).
4. Open your Railway builder domain and sign up with your admin email.
5. Create your first bot in the visual builder and deploy to the viewer service.

## Environment Variables

| Variable | Description |
|----------|-------------|
| `ENCRYPTION_SECRET` | 32-character secret for encryption. Auto-generated. |
| `DATABASE_URL` | Auto-set from Railway Postgres |
| `REDIS_URL` | Auto-set from Railway Redis |
| `NEXTAUTH_URL` | Public URL of builder service (required for auth callbacks) |
| `NEXT_PUBLIC_VIEWER_URL` | Public URL of viewer service (required for bot embedding) |
| `ADMIN_EMAIL` | Email address for initial admin account |
| `NODE_OPTIONS` | Set to `--no-node-snapshot` for compatibility |

## Architecture

This template deploys two services:

- **Builder** (port 3000) — Drag-and-drop bot editor and admin dashboard.
- **Viewer** (port 3000) — Lightweight service that renders published bots.

Both services share a PostgreSQL database and Redis cache over private networking.

## Adding the Viewer Service

After deploying the builder:

1. In your Railway project, click **+ New** → **Service from GitHub repo**
2. Select your Typebot repo
3. Set `RAILWAY_SERVICE_NAME` env var to `viewer`
4. Change the Dockerfile to `Dockerfile.viewer`
5. Generate a new public domain for the viewer

Then set `NEXT_PUBLIC_VIEWER_URL` on the builder to point to the viewer's public domain.

## Notes

- This template uses the official `baptistearno/typebot-builder` and `baptistearno/typebot-viewer` Docker images.
- PostgreSQL and Redis services are added automatically and communicate via private networking.
- First deployment may take 2-3 minutes due to database initialization.
- For production use, configure email variables for password resets and notifications.
- Ensure both `NEXTAUTH_URL` and `NEXT_PUBLIC_VIEWER_URL` are set to public Railway domains for proper functionality.
