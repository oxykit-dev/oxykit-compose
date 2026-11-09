# oxykit-compose

Docker Compose stack for the [OxyKit](https://oxykit.dev) suite: **Render + Mail + Flow** from published images on GHCR. No .NET SDK or sibling checkouts needed.

| Service | Image | Port |
|---------|-------|------|
| Render | `ghcr.io/oxykit-dev/oxykit-render` | 5066 |
| Mail | `ghcr.io/oxykit-dev/oxykit-mail` | 5026 |
| Flow | `ghcr.io/oxykit-dev/oxykit-flow` | 5077 |

Each core builds and publishes its own image (`Dockerfile` + `.github/workflows/docker.yml` in [OxyKit.Render](https://github.com/oxykit-dev/OxyKit.Render), [OxyKit.Mail](https://github.com/oxykit-dev/OxyKit.Mail), [OxyKit.Flow](https://github.com/oxykit-dev/OxyKit.Flow)). Tags: release versions (`0.1.7`, `0.1`, `latest`), `edge` (default branch), `sha-<short>`.

## Quick start

```bash
cp .env.example .env      # Jwt__Key + SMTP (Mail needs external SMTP; no SMTP container)
docker compose up -d
./scripts/bootstrap-mail.sh   # mints a Mail API key into FLOW_MAIL_API_KEY, recreates Flow
```

Pin image versions with `OXYKIT_RENDER_TAG`, `OXYKIT_MAIL_TAG`, `OXYKIT_FLOW_TAG` in `.env`.

- Health: `http://localhost:5066/health`, `:5026/health`, `:5077/health`. Images run `Production`, so Scalar is off unless you set `ASPNETCORE_ENVIRONMENT=Development`.
- Console: [oxykit-console](https://github.com/oxykit-dev/oxykit-console) (`npm run dev`, `:5173`) — not a compose service.

## Smoke test

```bash
./scripts/e2e.sh   # starts the stack, Flow → Render → file, then Flow → Mail (accept)
```

`RUN_MAIL=0` skips the mail leg; `START_COMPOSE=0` uses an already-running stack. Mail bootstrap expects a fresh `mail-data` volume (or `ADMIN_USER` / `ADMIN_PASSWORD` matching the existing admin).

## Local builds

With Render, Mail and Flow checked out next to this repo:

```bash
docker compose -f compose.yml -f compose.dev.yml up --build
# or the smoke test against local builds:
COMPOSE_FILE=compose.yml:compose.dev.yml ./scripts/e2e.sh
```

`OXYKIT_SRC` overrides the parent folder of the checkouts (default `..`).

## CI

[`.github/workflows/e2e.yml`](.github/workflows/e2e.yml): pinned tags on push/PR, `edge` nightly, any tag via manual dispatch.

Docs: [oxykit.dev/guide/compose](https://oxykit.dev/guide/compose).
