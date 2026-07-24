# Deploy and Host self-hosted Typebot on Railway

Typebot is an open-source visual chatbot builder that lets you design conversational flows without writing code. It replaces Intercom, Drift, and Chatfuel with a self-hosted stack you fully own — your bot flows, your customer conversations, your data. This template deploys the whole thing on Railway: builder, viewer, database, and file storage, wired together and ready to use in a few minutes.

## About Hosting self-hosted Typebot

Here's the math that makes self-hosting worth it: Intercom's Advanced plan starts around $85/month per seat before you've sent a single conversation. Drift's pricing isn't even public — you have to talk to sales. Typebot self-hosted on Railway runs $5-15/month total, covering compute, database, and file storage, with no per-conversation fees and no seat limits. That's not a rounding error. For a small team running a handful of support and lead-gen bots, it's the difference between a real budget line item and something you have to justify every quarter.

Self-hosting also means your customer conversations never leave infrastructure you control. If your legal team has opinions about where support chat transcripts live, or you're in a regulated industry where "we store this on a third party's servers" isn't an acceptable answer, self-hosting solves that at the infrastructure level rather than the contract-negotiation level. Railway handles the parts you don't want to think about — provisioning PostgreSQL, wiring up MinIO for file storage, generating SSL certificates, keeping services on a private network so your database is never reachable from the public internet. You get the ownership of self-hosting without the weekend lost to Docker Compose debugging.

This template deploys six services automatically: the builder (where you design bots), the viewer (where published bots run), PostgreSQL (all persistent data), MinIO (S3-compatible storage for images and files uploaded mid-conversation), a one-time bucket-setup job, and Valkey (included for parity with the official reference template, though Typebot itself doesn't currently depend on it). The only manual step is creating a free GitHub OAuth App for login — Typebot has no default authentication, so this is unavoidable regardless of how you deploy it, and it takes about a minute.

## Common Use Cases

- **Automated customer support** — Handle repetitive FAQs and route complex issues to a human agent, 24/7, without adding headcount. A well-designed support bot can resolve 30-50% of incoming questions before a human ever sees the ticket.
- **Lead qualification** — Ask a handful of branching questions to filter tire-kickers from real prospects before your sales team spends time on a call. Conversational forms consistently outperform static forms on completion rate — people finish a chat-style flow that they'd abandon halfway through a 12-field form.
- **Post-purchase surveys** — Collect feedback right after checkout, while the experience is fresh, instead of a generic "how did we do?" email three days later that nobody opens.
- **Appointment booking** — Walk a user through picking a service, a time slot, and confirming details, without paying for a dedicated scheduling SaaS on top of everything else.
- **Structured onboarding** — Replace a wall of documentation with an interactive walkthrough that adapts based on what the user actually needs, cutting time-to-first-value for new signups.
- **Sales enablement** — Let prospects self-serve pricing and feature questions through a chat widget instead of gatekeeping that information behind a "book a demo" form.
- **Internal tools** — HR intake forms, IT support triage, or feedback collection for internal teams, where a chat interface is genuinely friendlier than another Google Form nobody wants to fill out.

Is a chatbot the right tool for all of these? Not always — a single yes/no question is faster as a plain form. Typebot earns its place with real branching logic: different paths for different answers, conditional questions, data that flows into a CRM based on what the user says.

## Dependencies for self-hosted Typebot

The official Typebot Docker images need PostgreSQL for everything persistent — bot configurations, published flows, user accounts, and full conversation history. Media uploaded during a conversation (images, files a user attaches, or media a bot sends back) needs S3-compatible object storage; this template provisions MinIO for that automatically, so there's no separate AWS account or S3 bucket to set up by hand.

### Deployment Dependencies

- PostgreSQL 16, via Railway's managed `postgres-ssl` plugin, for all transactional data
- MinIO, pinned to a specific release rather than `latest`, for S3-compatible media storage
- A one-time MinIO bucket-creation job (safe to delete after it succeeds — it doesn't need to keep running)
- Typebot builder image, `baptistearno/typebot-builder:3.17.2`, for the visual editor
- Typebot viewer image, `baptistearno/typebot-viewer:3.17.2`, for executing published bots
- A free GitHub OAuth App, created by you, for logging into the builder

### Implementation Details

Typebot ships as two separate Next.js applications sharing one PostgreSQL database over Railway's private network. The builder handles bot design, publishing, and account management; the viewer is a lighter-weight service whose only job is rendering a published bot's conversation flow to end users. Both need to bind to `0.0.0.0` rather than `localhost` to be reachable by Railway's networking layer — the exact `HOSTNAME=0.0.0.0` and `PORT=3000` values are pre-wired in this template so you don't have to debug that yourself. Environment variables auto-configure the database connection, the shared encryption key both services need to agree on, and the MinIO credentials for file uploads.

## How self-hosted Typebot compares to the alternatives

**Vs. Intercom or Drift** — both are excellent products, and both charge accordingly. You're renting someone else's infrastructure and paying a recurring premium for it, indefinitely. Self-hosted Typebot has a real setup cost (this template gets you most of the way there) but no ongoing per-seat or per-conversation charges after that.

**Vs. Landbot or Tidio** — similar visual-builder experience, same SaaS pricing model problem. The practical question is whether owning your data and avoiding recurring fees is worth the setup effort. For a small team already comfortable with Railway, it usually is.

**Vs. building a custom chat widget from scratch** — Typebot gives you branching logic, conditional questions, webhooks, and a visual editor non-technical teammates can actually use, out of the box. Building that yourself is weeks of work.

## Getting Your First Bot Live

Once the template finishes deploying and you've logged in via GitHub, the fastest path to a working bot: create a new bot, drag in a "Text" bubble for your opening message, add a "Choice" or "Text Input" block, and connect them. Preview the flow before publishing to catch broken logic early. Hit Publish, and the bot goes live at your viewer URL immediately — no separate deploy step.

From there, most teams add a webhook block to send answers to a CRM or Slack, and embed the bot via the provided snippet — a script tag, iframe, or popup bubble. Conversations and uploaded files persist in this template's Postgres and MinIO automatically.

## Why Deploy self-hosted Typebot on Railway?

Railway is a singular platform to deploy your infrastructure stack. Railway will host your infrastructure so you don't have to deal with configuration, while allowing you to vertically and horizontally scale it.

By deploying self-hosted Typebot on Railway, you get a complete application stack provisioned in one click — builder, viewer, database, and file storage, already wired together. Railway handles multi-service orchestration, automatic SSL, private networking, healthchecks, and automatic restarts, cutting out the operational overhead of hand-rolling a Docker Compose stack on a bare VPS or wrestling with AWS ECS and RDS just to get a chatbot builder running.

## Frequently Asked Questions

### Do I need to know how to code to use Typebot?
No. The builder is entirely drag-and-drop — you connect blocks representing questions, conditions, and actions on a visual canvas. Code execution blocks exist for advanced use cases, but they're optional, not required.

### Why does this template need a GitHub OAuth App?
Typebot has no built-in login system of its own — it delegates authentication to an external provider (GitHub, Google, or email/SMTP). This template uses GitHub OAuth because it's the fastest path to a working login for anyone who already has a GitHub account, with no email service to configure. It's a one-time, roughly one-minute setup.

### Can bots send images or files to users?
Yes, through the MinIO service this template provisions automatically. Uploaded media gets a public URL so it can be sent back to whoever's chatting with the bot — no separate S3 bucket or AWS account required.

### How much does this actually cost to run?
Budget $5-15/month for typical usage, covering the builder, viewer, database, and file storage together. Cost scales gradually with conversation volume and stored media, not per-seat or per-conversation like the commercial alternatives.

### What happens if I skip the GitHub OAuth setup?
The template still deploys successfully and passes its healthchecks — but you won't be able to log into the builder at all, since there's no other authentication method configured. If you hit this, it means the setup step was missed, not that anything is broken.

### Can I connect a custom domain?
Yes, via Railway's own domain settings on the builder and viewer. Just update your GitHub OAuth App's callback URL to match — the login flow checks it exactly.
