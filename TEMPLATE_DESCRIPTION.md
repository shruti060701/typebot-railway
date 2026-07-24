## Template Titles

**Railway Title:** `Typebot [Updated Jul '26]`
**Railway Description:** `Typebot [Jul '26] (Visual Chatbot Builder & Conversational AI) Self Host`
**Spreadsheet Title:** `Typebot (Open-Source Visual Chatbot Builder & Flow Automation Platform)`
**GitHub Description:** `Typebot — open-source visual chatbot builder. Deploy on Railway with one click.`

---

![Typebot banner image](https://res.cloudinary.com/dm9xq7pqa/image/upload/v1720000000/typebot-banner.png "Hosting Typebot on Railway")

# Deploy and Host self hosted Typebot (Open-Source Chatbot Builder) on Railway

Typebot is an open-source chatbot builder that lets you create conversational experiences without touching a single line of code. It replaces expensive platforms like Intercom and Drift with a self-hosted solution giving you complete control over bot data, conversation history, and user interactions. Drag and drop conditional logic, integrate APIs, collect leads, and embed bots across websites in minutes.

## About Hosting Typebot open-source software on Railway (self hosted Typebot template)

Self-hosting Typebot on Railway means your bot flows and customer data stay under your control forever. Railway manages infrastructure complexity—provisioning PostgreSQL and MinIO storage, and networking between builder and viewer—while you focus on designing better conversations. No vendor lock-in, no per-conversation fees.

## Why Deploy Typebot, the Intercom alternative on Railway (Railway Free Trial)

Typebot delivers the conversational AI features of Intercom and Drift for zero licensing cost. You pay only for compute and storage on Railway. New users get a $5 free trial, covering a fully-featured Typebot instance for weeks of testing and small-scale production use.

Railway is a singular platform to deploy your infrastructure stack. Railway will host your infrastructure so you don't have to deal with configuration, while allowing you to vertically and horizontally scale it.

### Railway vs Other Hosting Providers and VPS for Typebot self hosting

| Provider          | What You Get with Railway           | What You Get with the Other Provider     |
| ----------------- | ----------------------------------- | ---------------------------------------- |
| **DigitalOcean**  | Instant multi-service setup, built-in secrets, automatic SSL, zero-config networking | Manual Docker Compose, manual Nginx, self-managed SSL certificates, complex networking |
| **AWS**           | Simple service discovery, one-click database provisioning, instant public domains | Complex EC2, RDS, ElastiCache configuration with IAM overhead |
| **Hetzner**       | One-click deploy with Postgres and MinIO auto-provisioned | Raw VPS requiring manual OS setup, Docker installation, and Compose orchestration |

## Common Use Cases for hosted Typebot

- **Customer support chatbots** — Handle FAQs, route tickets, and collect user issues 24/7.
- **Lead generation & qualification** — Qualify prospects with conditional flows before sales handoff.
- **Feedback collection** — Deploy surveys and feedback bots to improve your product.
- **Onboarding flows** — Guide new users through setup with interactive conversations.
- **Sales enablement** — Let prospects explore pricing, features, and plans through chat.

![Typebot builder screenshot](https://res.cloudinary.com/dm9xq7pqa/image/upload/v1720000001/typebot-builder.png "Typebot visual flow builder")

## Dependencies for Typebot Docker hosted on Railway

Typebot is built as a Next.js application requiring PostgreSQL for persistent bot data, user accounts, and conversation history, and MinIO (S3-compatible) storage for media uploaded during bot conversations. Official Docker images include both the visual builder and a lightweight viewer that renders published bots. Services communicate via private networking.

### Deployment Dependencies for Managed Typebot Service (OSS Chatbot Platform)

The Railway template provisions the builder and viewer on port 3000 each, PostgreSQL for all data persistence, and MinIO (S3-compatible storage) for media uploaded in bot conversations. Builder and viewer share the same database and communicate over private networking.

### Implementation Details for Typebot (Using Typebot official docker images)

The template deploys `baptistearno/typebot-builder:3.17.2` and `baptistearno/typebot-viewer:3.17.2`, pinned to a specific version rather than `latest`. Postgres and MinIO connections are auto-configured. Login uses GitHub OAuth — the only manual step is creating a free GitHub OAuth App and providing its two credentials.

## Environment Variables Reference for Typebot on Railway

| Variable | Description | Value |
|----------|-------------|-------|
| `GITHUB_CLIENT_ID` | Required for login. From a free GitHub OAuth App (github.com/settings/developers). | User-provided |
| `GITHUB_CLIENT_SECRET` | Required for login. Paired with the Client ID above. | User-provided |
| `ENCRYPTION_SECRET` | 32-character secret for data encryption. Do not share. | `${{secret(32)}}` |
| `NEXTAUTH_SECRET` | Secret key for authentication sessions. Auto-generated. | `${{secret(32)}}` |
| `NEXTAUTH_URL` | Public URL of the builder service for auth callbacks. | `https://${{RAILWAY_PUBLIC_DOMAIN}}` |
| `NEXT_PUBLIC_VIEWER_URL` | Public URL of the viewer service for bot embedding. | `https://viewer-domain.railway.app` |
| `DATABASE_URL` | PostgreSQL connection string (auto-injected from Postgres). | `${{Postgres.DATABASE_URL}}` |
| `S3_ACCESS_KEY` / `S3_SECRET_KEY` / `S3_BUCKET` / `S3_ENDPOINT` | MinIO credentials for media uploads. Auto-wired. | `${{minio.MINIO_ROOT_USER}}` etc. |

## How does Typebot compare against other chatbot builders

### Typebot vs Intercom (Intercom Alternative)
* **Cost model:** Typebot is free to self-host; Intercom charges per monthly active user with tiers starting at $99/month.
* **Data ownership:** Typebot keeps conversations and data under your control; Intercom stores on their servers.
* **Setup complexity:** Typebot deploys in minutes via Railway template; Intercom requires account setup and API configuration.

### Typebot vs Drift (Drift Alternative)
* **Pricing:** Typebot has no per-conversation fees; Drift starts at $500/month for conversation management.
* **Customization:** Typebot offers full source code access for custom modifications; Drift has limited API customization.
* **Deployment:** Typebot self-hosted on your infrastructure; Drift is a managed SaaS platform only.

### Typebot vs Dialogflow (Dialogflow Alternative)
* **Ease of use:** Typebot visual builder requires zero coding; Dialogflow requires NLP training and intent setup.
* **Integration ecosystem:** Typebot supports webhooks, Zapier, and direct APIs; Dialogflow focuses on Google ecosystem.
* **Embedding:** Typebot bots embed in one line of code; Dialogflow requires custom frontend development.

## How to use Typebot (the OSS Chatbot Builder Platform)?

Deploy the template, open your builder domain, create an account, drag flows in the visual editor, and share the bot link or embed code.

## How to self host Typebot on other VPS Services (Typebot self hosting guide)

### Clone the Repository
Clone the official Typebot repository from GitHub to access Docker Compose configuration and environment templates.

### Install Dependencies
Install Docker and Docker Compose. Ensure you have at least 2GB RAM and 10GB storage.

### Configure Environment Variables
Copy the `.env.example` file to `.env` and set encryption, database, auth, and URL variables.

### Start the Typebot Application
Run `docker compose up -d` to start all services. Builder will be available after health checks complete.

## Official Pricing of Typebot (Typebot pricing)

Typebot is AGPL 3.0 licensed—free to self-host, no licensing fees, per-conversation charges, or feature restrictions. Typebot Cloud offers managed hosting for teams preferring not to manage infrastructure.

## Typebot cloud vs self hosted comparison (Pricing, features, costs, and more)

Self-hosting gives full access to all features with no usage limits and complete data control. The cloud version removes infrastructure management but introduces per-message pricing and outside data residency.

### Monthly cost of self hosting Typebot on Railway

Expect $5-15/month for a single builder and viewer instance depending on bot traffic and conversation volume. This includes PostgreSQL and MinIO storage. High-traffic instances may cost $20-40/month, still substantially less than Intercom's $99/month minimum.

### System Requirements for Hosting Typebot on a VPS

Minimum specs: 2GB RAM, 1 vCPU, 20GB storage for Typebot, Postgres, and MinIO. For production use with 100+ daily conversations, allocate 4GB RAM, 2 vCPU, and 50GB storage with backup capacity.

## Frequently Asked Questions (FAQs)

### What is Typebot self hosted?
Typebot self-hosted is the open-source version running on your infrastructure with the same builder, flows, and integrations as cloud without fees or vendor lock-in.

### How much does Typebot self hosting cost on Railway?
Railway charges for compute, storage, and bandwidth. A typical Typebot setup costs $5-15/month depending on bot traffic and database size.

### Is Typebot free to use?
Yes, Typebot is open-source under AGPL 3.0 and free to self-host. You pay only for Railway infrastructure like compute, storage, and bandwidth.

### What integrations does Typebot support?
Typebot supports webhooks for custom APIs, Zapier automation, email notifications, file uploads, variables, conditional logic, and script embedding on websites.

### Where can I download Typebot?
Download the open-source code from GitHub at `baptisteArno/typebot.io` or deploy instantly on Railway using this template.

### What are some alternatives to Typebot?
Intercom, Drift, Dialogflow, Zendesk, Freshchat, and Make offer chatbot features, though most require SaaS subscriptions.
