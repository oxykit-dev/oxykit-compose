# AGENTS.md — oxykit-compose

Deployment only: Compose manifests, image pins, bootstrap + smoke scripts for **Render + Mail + Flow**. No application code; Dockerfiles live in each core repo.

- `compose.yml` — GHCR images, tags from `OXYKIT_{RENDER,MAIL,FLOW}_TAG` (default `edge`). Ports 5066 / 5026 / 5077; same env keys as the cores (`Security__*`, `Render__Engines__oxykit-render__*`, `OxyKit.Mail__*`).
- `compose.dev.yml` — override that builds `${OXYKIT_SRC:-..}/OxyKit.{Render,Mail,Flow}` with each repo's `Dockerfile`.
- `scripts/e2e.sh` — golden-path smoke (Flow → Render → file, Flow → Mail accept). `scripts/bootstrap-mail.sh` — admin + API key → `FLOW_MAIL_API_KEY` in `.env`.
- External SMTP only; Mail `/health` can be 503 without SMTP, healthchecks accept any HTTP response.
- Console (`oxykit-console`) is not a compose service.
- Never commit `.env`. Keep `.env.example` and `scripts/e2e.sh`'s generated `.env` in sync when env keys change.
- Image changes go in the core repo; changing a core's ports/env means updating `compose.yml` here and `oxykit-site/docs/guide/compose.md`.
