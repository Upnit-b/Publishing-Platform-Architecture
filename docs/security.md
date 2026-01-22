# Security

This document outlines the primary security controls implemented in the platform.

---

## 1) Reverse proxy hardening

- Nginx acts as the only public entrypoint.
- The API runs behind Nginx; Express is configured with `trust proxy`.

---

## 2) HTTP headers

The API uses **Helmet** to apply secure defaults.

Key goals:
- reduce XSS risk
- limit MIME sniffing
- apply sensible defaults for modern browsers

---

## 3) CORS

CORS is configured to:
- allow only the trusted frontend origin
- allow credentials (cookies)
- allow standard methods and headers

---

## 4) Rate limiting

A global rate limiter protects the API from:
- brute force attempts
- basic abuse and scraping
- accidental client retry storms

Blocked events are logged (IP + path) for investigation.

---

## 5) Secrets & credentials

- Secrets are loaded from environment variables.
- No secrets should be stored in Git.

Recommended additions (if not already present):
- `.env.example` templates
- secret scanning in CI

---

## 6) File upload safety (Cloudinary)

Common safeguards:
- validate MIME type and file size
- store only Cloudinary URLs/public IDs in the database
- avoid serving arbitrary local files

---

## 7) Authentication & authorization

- Role-based access control (`ADMIN` vs `USER`).
- Protected API endpoints must validate session/auth.

(Implementation details vary by environment; this case study describes the high-level approach.)
