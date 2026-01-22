# OpenAPI (API Contract)

This repo includes an **OpenAPI 3.0** contract describing the backend API:

- `openapi/openapi.yaml`

## No refresh tokens

This system does **not** use refresh tokens. Auth is represented as:
- a **Bearer JWT** (Authorization header), **or**
- an **HttpOnly cookie** set by the server during login

The OpenAPI spec models both by returning an optional `token` in `AuthResponse` (some deployments return it in-body),
while still documenting protected routes via `bearerAuth`.

## Render interactive docs locally

### Redoc (fastest)
```bash
npx redocly preview-docs openapi/openapi.yaml
```

### Swagger UI (Docker)
```bash
docker run --rm -p 8080:8080 \
  -e SWAGGER_JSON=/openapi.yaml \
  -v $(pwd)/openapi/openapi.yaml:/openapi.yaml \
  swaggerapi/swagger-ui
```

Open http://localhost:8080
