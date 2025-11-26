# Monitoring Stack (Prometheus + Grafana)

 GitHub-ready stack for Prometheus, Grafana, node-exporter, and cAdvisor. Keep secrets in `.env` (not committed).

## Files
- `docker-compose.yml` – Stack definition. Prometheus config is bind-mounted from `prometheus/prometheus.yml`. cAdvisor stays internal (no host port) since Prometheus scrapes it over the network. Prometheus and Grafana are routed via Traefik (no direct host ports).
- `prometheus/prometheus.yml` – Scrape config; edit here when adding targets.
- `grafana/provisioning/datasources.yml` – Auto-wires Prometheus into Grafana.
- `grafana/provisioning/dashboards.yml` – Tells Grafana to load dashboards from `grafana/dashboards/`.
- `grafana/dashboards/` – Place exported Grafana dashboard JSON files here.
- `.env.example` – Copy to `.env` and set real secrets.

## Quick start
1. Copy env template and set real values (keep `.env` out of Git):
   ```sh
   cp .env.example .env
   # edit GF_SECURITY_ADMIN_USER / GF_SECURITY_ADMIN_PASSWORD
   ```
2. Ensure the external network name in `docker-compose.yml` matches your Traefik/n8n network (default `proxy`).
3. Deploy via Portainer or Docker:
   ```sh
   docker compose up -d
   ```
4. Access (via Traefik):
   - Prometheus: http://prometheus.duploflow.com (adjust host if needed)
   - Grafana: http://grafana.duploflow.com (user/pass from `.env`)

## Updating scrape targets
1. Edit `prometheus/prometheus.yml`.
2. Commit and push.
3. On the server: `git pull` and restart Prometheus (e.g., `docker restart prometheus` or via Portainer). Prometheus will load the new config.

## Optional targets
- `n8n` and `traefik` jobs are included in `prometheus/prometheus.yml`; comment or adjust to match your service names and ports.

## Why this avoids lost configs
- `docker-compose.yml` documents the `prometheus/prometheus.yml` mount.
- `.env` keeps credentials out of Git.
- README reminds how to deploy and update so Future-You never forgets.
