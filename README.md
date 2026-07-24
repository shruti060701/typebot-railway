# Typebot on Railway

Typebot — open-source visual chatbot builder. Deploy on Railway with one click.

[![Deploy on Railway](https://railway.app/button.svg)](https://railway.app/new/template/TYPEBOT_TEMPLATE_CODE)

## Features

- **Visual chatbot builder** — No-code interface to design and manage conversational flows.
- **Media uploads included** — MinIO (S3-compatible storage) provisioned automatically, so bots can send images/videos back to users mid-conversation.
- **Database included** — PostgreSQL provisioned automatically.
- **Multi-channel deployment** — Embed bots on websites, WhatsApp, Telegram, and more.
- **Self-hosted control** — Complete data ownership with zero vendor lock-in.
- **Flow builder** — Conditional logic, variable storage, API integrations, and webhooks.

## Before You Deploy: You Need a GitHub OAuth App

**This is the one required setup step — Typebot has no default login, so you need to create this first.** It takes about a minute and costs nothing:

1. Go to **github.com/settings/developers** → click **"New OAuth App"**
2. **Application name:** anything, e.g. `My Typebot`
3. **Homepage URL:** you won't have your Railway domain yet on first setup — use a placeholder like `https://example.com`, you can fix it after deploying
4. **Authorization callback URL:** same placeholder for now, e.g. `https://example.com/api/auth/callback/github` — **you'll need to come back and update this once Railway gives you your real domain** (Settings → OAuth Apps → your app → edit the callback URL)
5. Click **"Register application"** — GitHub shows you a **Client ID** immediately, and a **Client Secret** you generate on the same page (shown once, copy it now)
6. Enter both values when Railway's deploy wizard asks for `GITHUB_CLIENT_ID` and `GITHUB_CLIENT_SECRET`

If you skip this, the template still deploys successfully and passes its healthchecks — but you won't be able to log into the builder at all, since there's no other login method configured. This isn't a bug if you hit it; it means this step was missed.

## How to use

1. Create the GitHub OAuth App above first (or come back and add the two values after — either order works, but you can't log in until it's done).
2. Click the **Deploy on Railway** button.
3. Fill in `GITHUB_CLIENT_ID` and `GITHUB_CLIENT_SECRET` from step 1 (everything else is auto-generated or auto-referenced between services — no other required input).
4. Wait for deployment to finish (Postgres migrations + MinIO bucket setup take a minute or two).
5. **Go back to your GitHub OAuth App settings and update the Homepage URL / Callback URL to your real Railway builder domain**, now that you have it.
6. Open your Railway builder domain, click "Continue with GitHub," and authorize.
7. Create your first bot in the visual builder, publish it, and it'll be live at your viewer domain automatically.

## Environment Variables

| Variable | Description |
|----------|-------------|
| `GITHUB_CLIENT_ID` | **Required.** From your GitHub OAuth App — see setup steps above. |
| `GITHUB_CLIENT_SECRET` | **Required.** From your GitHub OAuth App — see setup steps above. |
| `ENCRYPTION_SECRET` | Auto-generated. Must be identical on Builder and Viewer (handled automatically by the template). |
| `NEXTAUTH_SECRET` | Auto-generated session key (Builder only). |
| `DATABASE_URL` | Auto-set from the Postgres service. |
| `NEXTAUTH_URL` | Auto-set to each service's own public domain. |
| `NEXT_PUBLIC_VIEWER_URL` | Auto-set to the Viewer's public domain. |
| `S3_ACCESS_KEY` / `S3_SECRET_KEY` / `S3_BUCKET` / `S3_ENDPOINT` / `S3_SSL` | Auto-wired to the included MinIO service for media uploads — no setup needed. |
| `HOSTNAME` | Set to `0.0.0.0` — required for the app to be reachable, don't change. |
| `PORT` | Set to `3000` — don't change. |

## Architecture

This template deploys 6 services:

- **Builder** — drag-and-drop bot editor and admin dashboard.
- **Viewer** — renders published bots for end users.
- **Postgres** — shared database for both.
- **MinIO** — S3-compatible storage for uploaded media, with its own web console built in.
- **MinIO Bucket Creator** — a one-time job that sets up the storage bucket on first deploy. Safe to delete once its logs show it succeeded — it doesn't need to keep running.
- **Valkey** — included for architectural parity with the official Typebot reference template; not currently used by the application (Typebot's own configuration has no Redis/Valkey-dependent features enabled by default).

Everything communicates over Railway's private network except MinIO, which also needs a public domain so bots can send uploaded media back to end users outside Railway.

## Notes

- This template uses the official `baptistearno/typebot-builder:3.17.2` and `baptistearno/typebot-viewer:3.17.2` Docker images, pinned to a specific version rather than `latest`.
- Login is GitHub OAuth only by default. If you'd rather use email-based login instead, Typebot also supports SMTP — see [Typebot's self-hosting docs](https://docs.typebot.com/self-hosting/configuration) for the `SMTP_*` variables (note: Gmail is confirmed not to work as an SMTP provider on Railway).
- First deployment may take a couple of minutes due to database migrations and MinIO bucket setup.
