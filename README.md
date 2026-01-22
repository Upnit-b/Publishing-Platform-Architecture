# underthewraps.com — Production Publishing Platform

**Live site:** https://underthewraps.com
**Source code:** Private (client-owned / production system)

This repository is a **public, code-free technical document** describing the architecture, deployment, and engineering decisions behind a real production publishing platform I built and deployed for a client.

> Why no code here? The production repository is private to protect client IP and credentials. This repo exists to demonstrate **system design, deployment, security, and engineering depth**.

---

## What the platform does

- **Publishing workflow:** create/edit articles with a rich block editor (**Editor.js**), cover images, captions, and SEO fields
- **Content organization:** categories + subcategories
- **Media management:** image upload + metadata (alt text, titles) stored in **Cloudinary**
- **Engagement:** likes + comments
- **Payments:** **PayPal** checkout / donations with server-side verification and persisted payment records
- **Contact:** contact form delivery

---

## Tech stack

### Frontend
- **Next.js** (app) + **React**
- **Tailwind CSS**
- Editor: **Editor.js** (block-based authoring) + `editorjs-react-renderer`
- Observability: **Sentry (Next.js)**

### Backend
- **Node.js + Express** (TypeScript)
- **Prisma ORM**
- **PostgreSQL (Supabase)** as the database
- Upload pipeline: **multer** + **Cloudinary**
- Security: **Helmet**, **CORS**, **rate limiting**, secure cookies
- Logging: **Pino** + **pino-http**
- Observability: **Sentry (Node)**

### Infrastructure / Deployment
- **AWS EC2**
- **Docker + Docker Compose** (separate client + server services)
- **Nginx** as reverse proxy (frontend + backend routing)
- TLS/HTTPS terminated at Nginx (certificate management depends on environment)

---

## Architecture (high level)

- Browser requests hit **Nginx** on the EC2 instance.
- Nginx routes:
  - `https://underthewraps.com/` → **Next.js** container
  - `https://underthewraps.com/api/*` → **Express** container
- Express talks to:
  - **Supabase Postgres** via Prisma
  - **Cloudinary** for image storage
  - **PayPal APIs** for payment verification

Diagrams:
- System overview: [`diagrams/system-overview.mmd`](./diagrams/system-overview.mmd)
- Deployment view: [`diagrams/deployment.mmd`](./diagrams/deployment.mmd)
- Data model (ERD): [`diagrams/erd.mmd`](./diagrams/erd.mmd)

---

## What I built (scope)

- Full-stack feature development (frontend + backend)
- Database schema design and migrations (Prisma)
- Production deployment on EC2 using Docker Compose
- Nginx reverse proxy routing for client + API
- Security hardening (Helmet, CORS, rate limiting, trusted proxy)
- Observability (Sentry) + structured request logging (Pino)

---

## Proof of production work

- Live site: https://underthewraps.com
- Architecture + deployment docs in this repo
- Screenshots can be added under [`/screenshots`](./screenshots) (sanitized / non-sensitive UI only)

---

## Docs

- Architecture: [`docs/architecture.md`](./docs/architecture.md)
- Deployment (EC2 + Nginx + Docker Compose): [`docs/deployment.md`](./docs/deployment.md)
- Security notes: [`docs/security.md`](./docs/security.md)
- Performance + SEO: [`docs/performance-seo.md`](./docs/performance-seo.md)
- Engineering decisions & tradeoffs: [`docs/decisions.md`](./docs/decisions.md)

---

## Contact

**Upnit Banga**
If you’d like to review the private repo in a screening call, I can walk through the codebase and architecture live.
