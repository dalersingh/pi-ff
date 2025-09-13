# Automation and OrchestrDesign notes and best practices

- Idempotency: include a stable external_id (SKU, listing draft id) on create/publish so retries don't duplicate.
- Rate limits: **CRITICAL UPDATE** - More complex than initially estimated:
  - **Printify**: 600/min global + 100/min catalog + 200/30min publishing (multi-tier limits)
  - **Etsy**: 10k/day + 10/second + 2-hour penalty windows for violations
  - **Solution**: Implement queue management with progressive backoff and quota monitoring
- Webhook security: use HTTPS; sign/verify webhooks when supported (Printify supports HMAC signature). Gumroad Ping retries hourly up to 3 hours if non-200.
- Circuit breakers: Add failure detection and auto-recovery for external API dependencies
- Monitoring: Track API quota usage, webhook delivery rates, and error patterns Tools

Goal: keep costs tiny, maximize reliability, and enable near-zero-touch flows across Etsy, Printify, and Gumroad with clean webhooks and idempotent workers.

Scope covered

- Webhook intake (Gumroad Ping, Printify webhooks)
- OAuth/API calls (Etsy v3, Printify v1/v2)
- Scheduling (daily/weekly jobs)
- Retries, alerts, and simple state (SQLite/Postgres)

Shortlist (what fits this project best)

- n8n (recommended): open-source, self-host or cloud. Great for webhook intake and light glue. HTTP Request node supports any REST API; built-in Webhook trigger handles Gumroad Ping easily. Low/no monthly cost if self-hosted; cloud free tier is available with limits.
- Pipedream: generous free tier, fast to wire webhooks → REST/API calls with Node/Python components. Good for rapid iteration if you don’t want to manage hosting.
- Make or Zapier: polished UX; useful if you prefer fully-managed. Watch out for task/operation costs at scale; can erode margin.
- GitHub Actions / Cron + lightweight worker: good for scheduled batch tasks (price tests, renewals). Zero cost if public, minimal if private.
- Temporal / Airflow: powerful but heavy for this scope. Use later only if you need complex long-running workflows with durable timers.

Recommended baseline

- Webhook glue: n8n workflow for Gumroad Ping → queue/worker; Printify events → notify + reconcile orders; optional Slack/Email alerts.
- Workers: Python or Node scripts for Etsy/Printify heavy-lifting (create/publish listings, file uploads, pricing). Triggered by n8n HTTP/queue or cron.
- Scheduler: n8n cron nodes or system cron/GitHub Actions for daily/weekly/monthly jobs.

Design notes and best practices

- Idempotency: include a stable external_id (SKU, listing draft id) on create/publish so retries don’t duplicate.
- Rate limits: Etsy v3 requires proper Authorization and x-api-key headers; backoff on 429 with jitter. Printify has error-rate expectations (<5% of total requests) and will throttle abusive traffic.
- Webhook security: use HTTPS; sign/verify webhooks when supported (Printify supports HMAC signature). Gumroad Ping retries hourly up to 3 hours if non-200.
- State: store products, assets, and reconciliation logs in SQLite/Postgres. Keep a “listing_state” per SKU with timestamps.
- Costs: prefer self-hosted n8n or Pipedream free tier to keep the CA$200 month-1 constraint.

Gumroad Ping → n8n sketch

- Webhook node (POST) receives x-www-form-urlencoded payload
- Transform → push to queue or call worker endpoint
- Branch: send confirmation email via free SMTP or transactional email free tier
- Persist: insert into subscriptions table

Etsy digital flow sketch

- Worker: create draft listing → upload images → upload digital file → activate
- Guard: ensure digital file present before activation (Etsy will 400 if missing)
- Monitor: on failure, post to alert channel; retry with exponential backoff

Printify POD flow sketch

- Worker: create product (blueprint, provider, print_areas) → publish to Etsy
- Pricing: fetch base + shipping using Printify catalog/shipping endpoints; compute CAD price floor and margin
- Monitor: subscribe to product:publish:started and order:\* events

When to choose alternatives

- Make/Zapier: you want a UI-first approach with minimal infra; you accept per-operation fees.
- Pipedream: you want code-first components with generous free tier and hosted webhooks.
- Temporal/Airflow: complex, multi-step sagas that must survive restarts with precise replay and versioning.

## References & proofs

- Etsy v3 listings/files/images endpoints exercised in community client specs:
  - https://github.com/JaceBayless/betsy/blob/main/spec/betsy/shop_listing_spec.rb
  - https://github.com/JaceBayless/betsy/blob/main/spec/betsy/shop_listing_file_spec.rb
  - https://github.com/JaceBayless/betsy/blob/main/spec/betsy/shop_listing_image_spec.rb
- Printify publish to Etsy via API seen in real scripts/libraries:
  - https://github.com/th3ssalus/AI-Tshirts/blob/main/T-shirt%20Automator.py#L269-L275
  - https://github.com/lawrencemq/printipy/blob/main/printipy/tests/test_api_v1.py#L1642-L1652
- Gumroad Ping webhook format and usage:
  - Ping help page in OSS Gumroad app: https://github.com/antiwork/gumroad/blob/main/app/views/public/ping.html.erb
  - API initializer referencing Ping events: https://github.com/antiwork/gumroad/blob/main/config/initializers/api_v2_methods.rb
  - Event parser for Gumroad Ping in compensated: https://github.com/zinc-collective/compensated/blob/0.X/compensated-ruby/lib/compensated/gumroad/event_parser.rb
