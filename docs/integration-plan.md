# Integration Plan (Etsy, Printify, Gumroad, n8n)

Etsy Open API v3

- Auth: OAuth 2.0 Authorization Code with PKCE; headers: x-api-key + Authorization: Bearer {user_id.token}.
- Digital listing steps:
  1. POST /application/shops/{shop_id}/listings (draft)
  2. POST /application/listings/{listing_id}/images
  3. POST /application/listings/{listing_id}/files (digital download)
  4. PATCH /application/shops/{shop_id}/listings/{listing_id} (state=active)
- Guard: activation 400s if no digital file present.

Printify API v1/v2

- Auth: Personal Access Token or OAuth 2.0.
- Create product: POST /v1/shops/{shop_id}/products.json
- Publish to Etsy: POST /v1/shops/{shop_id}/products/{product_id}/publish.json (control images/title/variants/tags/shipping_template flags)
- Shipping: v2 endpoints provide shipping cost/handling time; use to compute price floors.
- Webhooks: subscribe to order and product events; verify via HMAC signature.

Gumroad

- Ping: x-www-form-urlencoded POST to your HTTPS endpoint; retried hourly up to 3 hours if non-200.
- Use to grant subscription access, send onboarding, update CRM.

**Gemini 2.5 Flash Image API (NEW)**

- Auth: x-goog-api-key header with Gemini API key
- Endpoint: POST https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-image-preview:generateContent
- Input: JSON with text prompt for image generation
- Output: Base64 encoded image data in response.candidates[0].content.parts[].inline_data.data
- Features: Text-to-image, image editing, style transfer, high-fidelity text rendering

n8n workflows (suggested)

- ping-intake: Webhook → transform → worker HTTP → DB insert → email confirmation.
- pod-publish: Manual or schedule → design assets → Printify create → publish → Etsy partner disclosure reminder.
- **automated-design-flow**: Trigger → Gemini 2.5 Flash Image generation → Printify upload → product creation → Etsy listing
- monitoring: Cron → check failed jobs, webhook error rates; alert on anomalies.
- **rate-limit-manager**: Queue incoming requests → quota check → progressive delay → API call → retry logic
- **circuit-breaker**: Monitor API health → detect failures → route to fallback → auto-recovery
- **webhook-delivery**: Receive webhook → verify signature → process → confirm delivery → retry on failure

## Enhanced POD Automation Workflow (NEW)

**Complete Design-to-Sale Pipeline**:

```
1. Keyword Research →
2. Gemini Image Generation →
3. Claude Copy Generation →
4. Printify Product Creation →
5. Etsy Listing Publication →
6. Performance Monitoring
```

**n8n Implementation Steps**:

1. **Trigger Node**: Schedule or manual trigger with niche/keyword input
2. **Gemini Image Node**: HTTP Request to generate design variations
3. **Image Processing**: Convert base64 to file, upload to Printify
4. **Copy Generation**: Claude API for titles/tags/descriptions
5. **Product Creation**: Printify API with generated assets
6. **Listing Creation**: Etsy API with AI disclosure
7. **Performance Tracking**: Store metrics for optimization

## Critical Rate Limit Implementation

**Multi-tier Queue Management**:

```
Printify Queue: 500 requests/min (buffer below 600 limit)
Catalog Queue: 80 requests/min (buffer below 100 limit)
Publishing Queue: 150 requests/30min (buffer below 200 limit)
Etsy Queue: 8 requests/second (buffer below 10 limit)
```

**Progressive Backoff Strategy**:

- 429 response: Wait retry-after header + jitter
- Etsy penalty: Pause queue for 2+ hours
- Circuit open: Route to manual fallback procedures

Environments & secrets

- .env.example with: ETSY_CLIENT_ID, ETSY_REDIRECT_URI, ETSY_SCOPES, PRINTIFY_TOKEN, GUMROAD_PING_SECRET(optional), DB_URL.
- Store refresh/access tokens encrypted at rest.

## Proof it works (third‑party)

- Etsy Open API v3

  - Community client exercising Etsy v3 listing endpoints (create draft, list by shop):
    - https://github.com/JaceBayless/betsy/blob/main/spec/betsy/shop_listing_spec.rb
  - Listing files/images endpoint shapes used in tests (paths match v3):
    - Files: https://github.com/JaceBayless/betsy/blob/main/spec/betsy/shop_listing_file_spec.rb
    - Images: https://github.com/JaceBayless/betsy/blob/main/spec/betsy/shop_listing_image_spec.rb

- Printify → Etsy publish

  - Script posting to publish endpoint (v1) in the wild:
    - https://github.com/th3ssalus/AI-Tshirts/blob/main/T-shirt%20Automator.py#L269-L275
  - Library tests covering products.publish_product → POST /v1/shops/{shop}/products/{id}/publish.json:
    - https://github.com/lawrencemq/printipy/blob/main/printipy/tests/test_api_v1.py#L1642-L1652

- Gumroad Ping webhook
  - Open-source Gumroad app code including the Ping help/page and references used by creators:
    - Ping page view: https://github.com/antiwork/gumroad/blob/main/app/views/public/ping.html.erb
    - API initializer text referencing Ping format: https://github.com/antiwork/gumroad/blob/main/config/initializers/api_v2_methods.rb
  - Third‑party payments lib parsing Gumroad Ping payloads (form-encoded with product_permalink gum.co):
    - https://github.com/zinc-collective/compensated/blob/0.X/compensated-ruby/lib/compensated/gumroad/event_parser.rb
