# Technical Architecture

Core components

- Agent/brain: a CLI/chatbot with PC access that calls workers and APIs.
- Workers: Node or Python scripts for Etsy/Printify/Gumroad.
- Webhook glue: n8n (self‑hosted or cloud) or Pipedream.
- DB: SQLite (local) → Postgres (later) for products, assets, logs.
- Secrets: .env locally; Secret Manager in prod.

Data model (minimal)

- products(id, type: digital|pod|subscription, sku, title, price_cad, status, created_at)
- assets(id, product_id, kind: pdf|png|zip|mockup, path/url, checksum)
- listings(product_id, channel: etsy|gumroad|printify, external_id, state, last_error)
- economics(product_id, fees_json, floor_price_cad, margin_pct)
- events(id, source, type, payload_json, processed_at)

Key flows

- Etsy digital: draft listing → images → digital file → activate.
- Printify: create product (blueprint/provider/print_areas) → publish to Etsy.
- Gumroad: subscription sale → webhook → grant access + email.

Scheduling

- Daily: check failed jobs, rotate thumbnails/tags, ETL metrics.
- Weekly: price tests, keyword refresh.
- Monthly: policy/fees audit; CA GST/HST threshold check.

Observability

- Structured logs, error dashboards, webhook retry monitoring.
- Alerts: email/Slack on 4xx/5xx bursts or publish failures.

Security & compliance

- OAuth securely (Etsy v3 PKCE); store refresh tokens encrypted.
- Verify webhooks (Printify HMAC); HTTPS everywhere.
- Respect Etsy AI disclosure + production partner requirements.
