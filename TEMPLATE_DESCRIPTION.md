## Template Titles

**Railway Title:** `Typebot [Updated Jul '26]`
**Railway Description:** `Typebot [Jul '26] (Visual Chatbot Builder & Conversational AI) Self Host`
**Spreadsheet Title:** `Typebot (Open-Source Visual Chatbot Builder & Flow Automation Platform)`
**GitHub Description:** `Typebot — open-source visual chatbot builder. Deploy on Railway with one click.`

---

![Typebot banner image](https://res.cloudinary.com/dm9xq7pqa/image/upload/v1720000000/typebot-banner.png "Hosting Typebot on Railway")

# Deploy and Host self hosted Typebot (Open-Source Chatbot Builder) on Railway

Typebot is an open-source chatbot builder for creating sophisticated conversational experiences without coding. It replaces expensive platforms like Intercom and Drift with a self-hosted solution giving you complete control over bot data, conversation history, and user interactions. Drag and drop conditional logic, integrate APIs, collect qualified leads, gather customer feedback, and embed bots across websites in minutes.

## About Hosting Typebot open-source software on Railway (self hosted Typebot template)

Self-hosting Typebot on Railway means your bot flows, conversations, and customer data stay under your control indefinitely. Railway manages infrastructure complexity—provisioning PostgreSQL for data persistence, Redis for session management, and load balancing between builder and viewer services—while you focus on designing better experiences. No vendor lock-in, no per-conversation fees, and full transparent access to your analytics and user data. Railway's private networking keeps your database completely invisible while your bots remain instantly available globally.

## Why Deploy Typebot, the Intercom alternative on Railway (Railway Free Trial)

Typebot delivers the conversational AI and chatbot features of Intercom and Drift for zero licensing cost. You pay only for the compute and storage you consume on Railway. New users receive a $5 free trial credit, which covers a fully-featured Typebot instance for weeks of testing and small production use without upfront cost.

Railway is a singular platform to deploy your infrastructure stack. Railway will host your infrastructure so you don't have to deal with configuration, while allowing you to vertically and horizontally scale it.

### Railway vs Other Hosting Providers and VPS for Typebot self hosting

| Provider          | What You Get with Railway           | What You Get with the Other Provider     |
| ----------------- | ----------------------------------- | ---------------------------------------- |
| **DigitalOcean**  | Instant multi-service setup, built-in secrets, automatic SSL, and zero-config networking | Manual Docker Compose, manual Nginx, self-managed SSL certificates, and complex networking setup |
| **AWS**           | Simple service discovery, one-click database provisioning, and instant public domains | Complex EC2, RDS, ElastiCache configuration with IAM and VPC overhead |
| **Hetzner**       | One-click template deploy with Postgres and Redis auto-provisioned | Raw VPS requiring manual OS setup, Docker install, and Compose orchestration |

## Common Use Cases for hosted Typebot

- **Customer support automation** — Handle FAQs and collect user issues 24/7 without adding support staff.
- **Lead generation and qualification** — Qualify prospects with conditional flows before passing to sales teams.
- **Product feedback collection** — Deploy surveys and feedback bots to improve your product continuously.
- **Appointment automation** — Automate scheduling without external booking tools through conversational flows.
- **Customer onboarding** — Guide new users through product setup with interactive step-by-step conversations.
- **Sales enablement** — Let prospects explore your pricing, features, and plans through bot interactions.

![Typebot builder screenshot](https://res.cloudinary.com/dm9xq7pqa/image/upload/v1720000001/typebot-builder.png "Typebot visual flow builder")

## Dependencies for Typebot Docker hosted on Railway

Typebot requires PostgreSQL for bot configuration, user accounts, and complete conversation history storage, plus Redis for session management and real-time updates. Official Docker images include both the visual builder interface and a separate lightweight viewer service for rendering published bots. All services communicate securely via private networking without exposing databases to the internet.

### Deployment Dependencies for Managed Typebot Service (OSS Chatbot Platform)

The Railway template provisions three core services automatically: Typebot builder on port 3000 for designing bots, PostgreSQL 16 for data persistence, and Redis for caching and session management. Both builder and viewer services share the same database and communicate over Railway's private internal networking. Additional optional services like SMTP for email notifications and S3-compatible storage can be configured after your initial deployment.

### Implementation Details for Typebot (Using Typebot official docker images)

The template deploys `baptistearno/typebot-builder:latest` for the visual editor interface on port 3000 and references `baptistearno/typebot-viewer:latest` for bot rendering and execution. Postgres and Redis connections are automatically configured via environment variables injected by Railway. The builder service initializes the database schema on first startup. Healthchecks verify service availability at the root path with 120-second timeout to accommodate database migrations and initialization.

## How does Typebot compare against other chatbot builders

### Typebot vs Intercom (Intercom Alternative)
* **Cost:** Typebot is free to self-host; Intercom charges $99/month minimum per user tier.
* **Data ownership:** Typebot keeps all data under your control; Intercom stores data on their servers.
* **Setup:** Typebot deploys in minutes via Railway; Intercom requires account and API configuration.

### Typebot vs Drift (Drift Alternative)
* **Pricing:** Typebot has no per-conversation fees; Drift starts at $500/month.
* **Customization:** Typebot offers full source code access; Drift has limited API customization.
* **Deployment:** Typebot self-hosted on your infrastructure; Drift is SaaS only.

### Typebot vs Dialogflow (Dialogflow Alternative)
* **Ease:** Typebot visual builder requires zero coding; Dialogflow requires NLP training and intent setup.
* **Integrations:** Typebot supports webhooks and Zapier; Dialogflow is Google-ecosystem focused.
* **Embedding:** Typebot bots embed in one line; Dialogflow requires custom development.

### Typebot vs Make (Make Alternative)
* **Focus:** Typebot specializes in bots; Make is general automation with chatbot as add-on.
* **UI:** Typebot is purpose-built for bots; Make's interface is complex for bot use cases.
* **Cost:** Typebot self-hosted is free; Make charges per operation.

## How to use Typebot (the OSS Chatbot Builder Platform)?

Deploy the template to Railway, open your builder domain, create an account, drag flows in the visual editor, and share the public bot link or embed code on your website.

## How to self host Typebot on other VPS Services (Typebot self hosting guide)

### Clone the Repository
Clone the official Typebot repository from GitHub to access Docker Compose configuration and environment templates for customization.

### Install Dependencies
Install Docker and Docker Compose on your VPS. Ensure you have at least 2GB RAM and 10GB storage for Typebot, Postgres, and Redis services.

### Configure Environment Variables
Copy `.env.example` to `.env` and set `ENCRYPTION_SECRET` with a 32-character random string, `DATABASE_URL` for Postgres connection, `REDIS_URL`, and public URLs for `NEXTAUTH_URL` and `NEXT_PUBLIC_VIEWER_URL`.

### Start the Typebot Application
Run `docker compose up -d` to start all services. The builder UI will be available on port 8080 after health checks pass.

## Official Pricing of Typebot (Typebot pricing)

Typebot is open-source under AGPL 3.0 and free to self-host. There are no licensing fees, per-conversation charges, or feature restrictions. Typebot Cloud offers managed hosting with usage-based pricing for teams preferring not to manage infrastructure.

## Typebot cloud vs self hosted comparison (Pricing, features, costs, and more)

Self-hosted gives full access to all features with no limits and complete data control. Cloud removes infrastructure management but adds per-message pricing and stores data outside your control.

### Monthly cost of self hosting Typebot on Railway

Expect $5-15/month for a single instance depending on traffic and conversation volume. Includes PostgreSQL storage and Redis caching. High-traffic instances may cost $20-40/month, still less than Intercom's $99/month minimum.

### System Requirements for Hosting Typebot on a VPS

Minimum: 2GB RAM, 1 vCPU, 20GB storage for Typebot, Postgres, and Redis. Production with 100+ daily conversations: 4GB RAM, 2 vCPU, 50GB storage plus backup capacity.

## Frequently Asked Questions (FAQs)

### What is Typebot self hosted?
Typebot self-hosted is the open-source version running on your infrastructure. You get the same builder, flow logic, and integrations as cloud without fees or vendor lock-in.

### How much does Typebot self hosting cost on Railway?
Railway charges for compute, storage, and bandwidth. A typical Typebot setup costs $5-15/month depending on bot traffic, database size, and optional services.

### Is Typebot free to use?
Yes, Typebot is open and free under AGPL 3.0. Self-hosting is free. You pay only for infrastructure like Railway compute, storage, and bandwidth.

### What integrations does Typebot support?
Typebot supports webhooks for custom APIs, Zapier automation, email notifications, file uploads, variables, conditional logic, and script tag embedding on websites.

### Where can I download Typebot?
Download open-source code from GitHub at `baptisteArno/typebot.io` or deploy instantly on Railway using this template.

### What are some alternatives to Typebot?
Intercom, Drift, Dialogflow, Zendesk, Freshchat, and Make offer chatbot features with various SaaS subscriptions or vendor lock-in.
