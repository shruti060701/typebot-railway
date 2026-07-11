# Deploy and Host self-hosted Typebot on Railway

Typebot is an open-source visual chatbot builder that lets you create conversational experiences without writing code. It replaces expensive platforms like Intercom and Drift with a self-hosted solution that gives you complete control over your bot data and customer conversations.

## About Hosting self-hosted Typebot

Self-hosting Typebot on Railway means your bot flows, user conversations, and customer data stay under your control. Railway handles all the infrastructure complexity—provisioning PostgreSQL for persistence, Redis for caching, and orchestrating the builder and viewer services—while you focus on designing better conversational experiences. You get zero vendor lock-in, no per-conversation pricing, and full transparency into how your application runs. Railway's private networking keeps your database invisible from the internet while your bots remain instantly available to customers worldwide.

## Common Use Cases

- Automated customer support handling FAQs and routing tickets without adding headcount
- Lead qualification flows that pre-qualify prospects before routing to sales teams
- Post-purchase surveys and feedback collection to continuously improve products
- Appointment booking automation without relying on external third-party booking platforms
- Structured customer onboarding guides that teach product features through interactive conversations
- Sales enablement through self-service bot interactions letting prospects explore your pricing and features

## Dependencies for self-hosted Typebot

The official Typebot Docker images require PostgreSQL for storing bot configurations, user accounts, and complete conversation history. Redis handles session management and real-time updates across the builder and viewer services.

### Deployment Dependencies

- PostgreSQL 16 for transactional data storage
- Redis (alpine) for caching and session management
- Typebot builder image for the visual editor interface
- Typebot viewer image for executing published bots

### Implementation Details

Typebot deploys as a Next.js application using `baptistearno/typebot-builder:latest` on port 3000 for the drag-and-drop editor and `baptistearno/typebot-viewer:latest` for rendering published bots. Both services share the same PostgreSQL database over private networking. Environment variables auto-configure database and Redis connections.

## Why Deploy self-hosted Typebot on Railway?

Railway is a singular platform to deploy your infrastructure stack. Railway will host your infrastructure so you don't have to deal with configuration, while allowing you to vertically and horizontally scale it.

By deploying self-hosted Typebot on Railway, you get a complete application stack provisioned instantly. Railway handles multi-service orchestration, automatic SSL certificates, private networking, healthchecks, and automatic restarts—eliminating the operational burden of managing Docker Compose on a VPS or configuring complex cloud infrastructure like AWS or DigitalOcean.
