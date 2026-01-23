# Engineering decisions & tradeoffs

This document highlights the key decisions made to ship and operate the platform reliably.

---

## Why Next.js for the frontend?
- Strong SEO story for content sites
- Flexible rendering (SSR/SSG/ISR) depending on page requirements
- Great developer ergonomics for building admin + public pages

## Why Express + TypeScript for the API?
- Clear separation of concerns (frontend vs API)
- Type safety for controllers/middleware
- Mature ecosystem (auth, uploads, security middleware)

## Why Supabase Postgres?
- Managed Postgres with a strong developer experience
- Easy environment separation and connection management
- Works well with Prisma

## Why Prisma?
- Schema-first modeling and migrations
- Strong TypeScript integration
- Safer querying than raw SQL for most app needs

## Why Cloudinary for images?
- Image transformations + CDN delivery
- Separates file storage concerns from the application server
- Only store URLs/public IDs in the DB (simplifies scaling)

## Why Docker Compose on EC2?
- Reproducible deployment
- Straightforward rollback (image pinning)
- Clear service boundaries (client vs server)

## Why Nginx reverse proxy?
- Single public entrypoint for both client and API
- TLS termination + HTTP/2
- Well understood, stable production pattern

---

## Tradeoffs

- **Single VM** (EC2) is simple and cost-effective, but not as resilient as multi-AZ.
- Compose is easy for small-to-medium deployments; Kubernetes may be overkill initially.
- Rate limiting helps, but advanced protection could include WAF + bot mitigation.

---

## Next improvements (roadmap ideas)

- Zero-downtime deploy strategy (blue/green or rolling)
- Stronger caching strategy for high-traffic content
