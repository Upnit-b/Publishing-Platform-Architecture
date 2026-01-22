# Performance & SEO

This platform is a content-driven site, so performance and SEO are first-class requirements.

---

## 1) Rendering strategy

- Next.js supports server rendering for fast first paint and crawlability.
- Pages can be structured to balance:
  - SSR for dynamic content
  - caching where appropriate

---

## 2) Images

Images are stored and delivered via **Cloudinary**, enabling:
- transformation (resize/compress)
- modern formats
- CDN-backed delivery

Recommended practices:
- always provide alt text for accessibility and SEO
- generate appropriately sized variants (avoid shipping huge originals)

---

## 3) API performance

- Prisma provides efficient DB access patterns.
- Indexing for common lookups (e.g., unique slugs) helps with article routes.

---

## 4) Caching

Opportunities:
- CDN caching for static assets/images
- response caching for high-traffic read endpoints
- Next.js route caching (if applicable)

---

## 5) Observability

- Sentry captures exceptions and traces (frontend + backend), reducing MTTR.
- Structured logging (Pino) supports debugging production issues quickly.

---

## 6) What I would measure (recommended)

- Lighthouse scores (Performance, Accessibility, SEO)
- Core Web Vitals (LCP/CLS/INP)
- API p95 latency for `/api/articles` and `/api/images`
- error rates and rate-limit blocks
