# Architecture

This document explains the platform architecture at a level that is **useful for recruiters/engineers** while keeping the production code and client IP private.

---

## 1) System overview

**Primary components**
- **Next.js frontend**: public site + admin/editor UI
- **Express API**: authentication, content APIs, image APIs, payments, contact
- **PostgreSQL (Supabase)**: persistent data storage
- **Cloudinary**: image storage + delivery
- **PayPal**: payment processing / verification
- **Nginx**: reverse proxy on EC2 (single public entrypoint)

See diagram: `diagrams/system-overview.mmd`.

---

## 2) Request flow

### Public browsing
1. User hits `https://underthewraps.com/...`
2. Nginx routes the request to the **Next.js** container.
3. For data needs (articles, categories, images), the Next.js app calls the API at `/api/*`.

### API calls
1. Browser → Nginx → Express `/api/*`
2. Express validates request (rate limiting, auth middleware where required).
3. Express uses **Prisma** to read/write to **Supabase Postgres**.

### Media uploads
1. Client uploads an image to the API.
2. API accepts multipart data (multer) and uploads to **Cloudinary**.
3. API stores the resulting `url` + `publicId` (+ metadata like title/altText) in Postgres.

### Payments
1. Client initiates PayPal checkout/donation.
2. Server verifies the PayPal transaction server-side.
3. Server writes a `Payment` record (transaction id, payer info if provided, amount, currency, status).

---

## 3) Auth & roles (high level)

- Users have roles (e.g., `ADMIN`, `USER`).
- Protected routes require a valid auth session.
- Cookies are used for session transport; API supports credentials-enabled CORS.

---

## 4) Data model (high level)

Core entities:
- `User` (role-based access)
- `Article` (Editor.js content JSON, cover image metadata)
- `Category` / `Subcategory`
- `Image` (Cloudinary URL + publicId + metadata)
- `Comment` / `Like`
- `Payment`
- `Subscriber`

See ERD diagram: `diagrams/erd.mmd`.

---

## 5) Observability

- **Sentry** captures exceptions and performance traces (frontend + backend).
- **Pino** structured logs for requests and errors.

---

## 6) Key implementation details (from the production codebase)

- Express is configured with `trust proxy` to work correctly behind Nginx.
- A global request rate limiter protects the API from abuse.
- Request payload limits are raised to support rich content + image metadata.
