# Deployment (EC2 + Nginx + Docker Compose)

This platform is deployed on **AWS EC2** using **Docker Compose**, with **Nginx** as the public reverse proxy for both the Next.js frontend and the Express API.

---

## 1) Components on the EC2 instance

- **Nginx** (host-level) listening on 80/443
- **Docker Compose** running two services:
  - `client` → Next.js on container port
  - `server` → Express API on container port

A production compose file in the codebase defines these services and uses separate `.env` files per service.

---

## 2) Networking & routing

**Public entrypoint:** `underthewraps.com` → EC2 → Nginx

**Routing rules**
- API → proxy to `server:PORT`
- everything else `/` → proxy to `client:PORT`


**Why `trust proxy` matters**
Because Nginx forwards requests, the Express app must trust the proxy to correctly derive:
- client IP (for rate limiting/logging)
- protocol (http vs https)

---

## 3) Build & run

Typical production flow:
1. Build containers from `client/Dockerfile` and `server/Dockerfile`
2. Start via docker compose
3. Nginx routes traffic to the containers

Example commands:

```bash
# build and run
docker compose -f docker-compose.prod.yml up -d --build

# view logs
docker compose -f docker-compose.prod.yml logs -f
```

---

## 4) Environment variables (high level)

Secrets are managed via environment variables (not committed):
- Database connection string (`DATABASE_URL`) for Supabase Postgres
- Cloudinary credentials
- PayPal client id/secret + webhook/verification configuration
- JWT/session secrets (if used)
- Sentry DSNs

---

## 5) Operational notes

- Restart policy is enabled in Compose (`restart: always`).
- API rate limiting reduces abuse.
- Logs should be shipped/rotated (CloudWatch / Loki / external logging) depending on environment.
