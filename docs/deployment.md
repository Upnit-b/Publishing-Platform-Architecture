# Deployment (EC2 + Nginx + Docker Compose)

This platform is deployed on **AWS EC2** using **Docker Compose**, with **Nginx** as the public reverse proxy for both the Next.js frontend and the Express API.

---

## 1) Components on the EC2 instance

- **Nginx** (host-level) listening on 80/443
- **Docker Compose** running two services:
  - `client` → Next.js on container port 3000
  - `server` → Express API on container port 5001

A production compose file in the codebase defines these services and uses separate `.env` files per service.

---

## 2) Networking & routing

**Public entrypoint:** `underthewraps.com` → EC2 → Nginx

**Recommended routing rules**
- `/api/*` → proxy to `server:5001`
- everything else `/` → proxy to `client:3000`

### Example Nginx reverse proxy (template)

> This is a **template**. In production you’ll adapt domains, SSL paths, and any caching.

```nginx
server {
  listen 80;
  server_name underthewraps.com www.underthewraps.com;

  # If you use certbot/letsencrypt, you typically redirect HTTP → HTTPS here.

  location /api/ {
    proxy_pass http://127.0.0.1:5001/;
    proxy_http_version 1.1;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
  }

  location / {
    proxy_pass http://127.0.0.1:3000;
    proxy_http_version 1.1;
    proxy_set_header Host $host;
    proxy_set_header X-Forwarded-Proto $scheme;
  }
}
```

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

Best practice is to maintain:
- `.env.example` (non-secret template)
- a secure secret store (or EC2-level env injection)

---

## 5) Operational notes

- Restart policy is enabled in Compose (`restart: always`).
- API rate limiting reduces abuse.
- Logs should be shipped/rotated (CloudWatch / Loki / external logging) depending on environment.
