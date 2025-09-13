here’s a crisp, “agent-ready” product spec that prioritizes the **highest probability, lowest-maintenance** paths to passive income within your budget (a couple hundred CAD up-front, minimal ongoing). it’s designed so an AI agent (plus APIs) can build and run it with **near-zero human touch** after initial setup.

---

# product spec — passive income automations (etsy digital + pod, gumroad membership)

## 0) objectives & guardrails

- **primary goal:** launch 2–3 passive, low-touch revenue streams that keep running after a one-time setup + occasional monthly check-ins.
- **constraints:**

  - low controversy niches only.
  - minimal recurring tool cost; fees must not erode margin.
  - runs mostly by API + chat-ops with PC access.

- **success metrics (90-day):**

  - ≥30 digital sales/month on Etsy (templates)
  - ≥20 POD sales/month with ≥25–35% gross margin (before income tax)
  - ≥20 Gumroad subscribers by month 3 (churn <5%/mo)

---

## 1) streams we will build (in order)

### Stream A — Etsy **digital downloads** (most passive, fastest to profit)

- **What:** auto-generated evergreen digital products (coding planners, cheatsheets, resume/portfolio kits, course outlines, fill-in templates, printable trackers, minimal wallpapers).
- **Why this first:** no inventory/shipping; pure digital fulfillment; Etsy auto-delivers files; your only costs are platform fees. Etsy provides automated fulfillment and collects/remits some taxes for Canada in many scenarios, reducing admin. ([Etsy Help][1])

### Stream B — Etsy + Printify **print-on-demand (POD)**

- **What:** trend-aware tees/mugs/posters using Printify’s API to create/publish products to your Etsy shop, auto-route orders to printers.
- **Why now:** scalable and mostly hands-off once listings + production partners are linked; Printify has full create/publish endpoints. ([developers.printify.com][2])

### Stream C — **Gumroad** micro-membership (recurring)

- **What:** recurring “coding practice pack” (monthly). Delivery is automated; Gumroad is a **Merchant of Record** (handles sales tax/VAT) and has simple webhooks (“Ping”) for automations. Fees are transparent. ([Gumroad][3])

---

## 2) policy, fees & taxes (canada-centric)

### Etsy (core fees you must model)

- Listing fee **US\$0.20** per listing (renews every 4 months). **Transaction fee 6.5%** of item (and shipping if any). **Payment processing (Canada)** typically **3% + CA\$0.25 (domestic)** or **4% + CA\$0.25 (international)**. **Currency conversion fee 2.5%** if your listing currency ≠ deposit currency — set both to CAD to avoid this. **Regulatory operating fee (Canada)** **1.15%** of item+shipping. Etsy also has optional **Offsite Ads** fees (**12%/15%**)—you can opt out if under US\$10k last-365-days. ([Etsy Help][4])
- Etsy may charge a **one-time shop setup fee** (varies by location; shown at shop creation). ([Etsy Help][4])
- Digital orders are delivered automatically; you can (and should) use “digital” fulfillment so no manual action is required.
- **Disclosure:** If you use a production partner (e.g., Printify) or AI in creation, Etsy requires you to **disclose** this in listings. ([Etsy][5])

### Printify (POD)

- API supports **create product**, **set print areas**, and **publish** to connected shops. Publishing mirrors title/description/images/variants. Shipping rates vary by print provider & destination; fetch via their docs/pages at runtime. ([developers.printify.com][2])

### Gumroad

- Pricing: **10% + US\$0.50** per direct sale; **30%** when sale is attributed to **Discover** (includes processing). As of **Jan 1, 2025**, Gumroad acts as **Merchant of Record** and handles sales tax globally. Webhooks (**Ping**) enable fully automated fulfillment/CRM. ([Gumroad][3])

### Books (optional later): KDP

- If you later automate low-content print books, note Amazon KDP **requires AI-content disclosure** and **reduced print royalties** (certain list-price tiers moved from 60% to **50%** on **June 10, 2025**). Use KDP’s royalty calculator when pricing. ([Kindle Direct Publishing][6])

### Canada GST/HST quick facts

- You’re a **small supplier** until **CA\$30,000** taxable supplies in **4 consecutive quarters**; once you exceed, you **must register** and charge GST/HST (timing rules apply). Etsy also collects/remits certain taxes on orders and on seller fees in Canada depending on your registration status—see their Canada tax page. ([Government of Canada][7])

---

## 3) unit economics (templates the agent must compute per SKU)

> Agent: always compute in **listing currency** (CAD), avoid currency-conversion fees by matching deposit currency to CAD.

### A) Etsy digital (no shipping)

**Price** – \[Transaction 6.5%] – \[Payment proc. 3% + CA\$0.25 (or 4% + CA\$0.25 if international)] – \[Regulatory 1.15%] – \[Listing fee amortization]
Example at **CA\$7.99** (domestic payment):

- Transaction: 7.99×0.065 = **0.51935**
- Proc.: 7.99×0.03 + 0.25 = **0.4897**
- Regulatory: 7.99×0.0115 = **0.091885**
- Listing amortization: assume 1 listing → 10 sales per term ⇒ \~US\$0.02 ≈ **\~CA\$0.03**
- **Estimated net per sale ≈ 7.99 − (0.51935+0.4897+0.091885+0.03) ≈ CA\$6.86** (before income tax).

_(Adjust for international cards at 4% and include Offsite Ads only if enabled.)_ ([Etsy Help][4])

### B) Etsy POD via Printify (customer pays shipping)

**Net = Item price + Shipping charged − Printify base − Printify shipping − Etsy fees (6.5% on item+shipping) − Payment proc. (3%+0.25 CA or 4%+0.25) − Regulatory 1.15% (on item+shipping) − listing amortization**
Agent must fetch **base price** for the chosen product/provider and **shipping** from the Printify catalog/shipping pages at runtime to compute price floors. ([developers.printify.com][2])

### C) Gumroad membership (monthly)

**Net = Price − (10% + US\$0.50)** (direct sales; Discover-attributed sales use **30%**). MoR tax handling is included by Gumroad. ([Gumroad][3])

---

## 4) break-even plan (conservative)

- **Assumptions:** initial tool spend ≤ **CA\$200** (first month), Etsy setup + \~20 listings (US\$0.20 each), Offsite Ads **OFF**, listing currency = CAD, digital example net ≈ **CA\$6.86**/sale as above.
- **BE target:** recover **CA\$200** in contribution margin.

  - Sales needed ≈ **200 / 6.86 ≈ 30** digital sales.
  - If 50% of payments are international (4% proc.), net might drop \~CA\$0.08–0.10 each → BE ≈ **31–32** sales.

- **Timeline target:** hit 30 digital sales within **first 30–45 days** by launching **30–50 SKUs** and daily auto-renew/testing.

---

## 5) technical architecture (automation-first)

### Components

- **Brain/Agent:** your AI chatbot (with PC access) orchestrating flows + prompting models.
- **Secrets vault:** .env or Secret Manager for API keys (Etsy OAuth, Printify, Gumroad).
- **DB:** lightweight SQLite/Postgres for products, assets, price floors, and logs.
- **Workers:** headless scripts (Node/Python) invoked by scheduler or webhooks.
- **No-code glue (optional):** n8n/Make/Zapier for Gumroad webhooks → CRM/email. ([n8n][8])

### Integrations & required scopes

- **Etsy Open API v3** (OAuth 2.0; `listings_w`, `shops_w`, etc.). Base: `https://api.etsy.com/v3/` (or `openapi.etsy.com`). Include `x-api-key` and `Authorization: Bearer …`. ([developers.etsy.com][9])
- **Key Etsy endpoints & flow (digital):**

  1. **Create draft listing**: `POST /application/shops/{shop_id}/listings` (aka `createDraftListing`).
  2. **Upload listing images**: `/listings/{listing_id}/images`.
  3. **Upload digital file**: `/listings/{listing_id}/files` (required to make a digital listing active).
  4. **Update → active**: `PATCH /application/shops/{shop_id}/listings/{listing_id}`.
     _(As of Jan 2025 Etsy fixed an issue; update to active will **400** if a downloadable file is missing—ensure file upload before activation.)_ ([developers.etsy.com][10])

- **Printify API** (Personal Access Token).

  - Create product: `POST /v1/shops/{shop_id}/products.json` (set `blueprint_id`, `print_provider_id`, `print_areas`).
  - Publish: `POST /v1/shops/{shop_id}/products/{product_id}/publish.json` (choose which fields to sync to Etsy). ([developers.printify.com][2])

- **Gumroad**

  - Webhooks: set **Ping endpoint** in settings; receive sale/subscription events (x-www-form-urlencoded). Use to auto-deliver bonus assets or trigger onboarding emails. ([app.gumroad.com][11])

---

## 6) automation flows (pseudo-SOPs the agent should execute)

### A) Digital listings (Etsy)

1. **Ideation:** generate evergreen, non-controversial template ideas with keyword difficulty < medium; produce 10/day.
2. **Asset gen:** create PDFs/PNGs/ZIPs; embed content credentials if available.
3. **Create draft listing via API** (title, tags, category/taxonomy, price, type=`download`, digital). Upload **mockup images** and **digital file**. Then **activate**. Ensure shop currency & deposit currency = CAD to avoid conversion fee. ([Etsy Help][4])
4. **Renew/AB-test:** rotate thumbnails, tags, and price floors weekly.
5. **Inbox policy:** respond to messages **once a month** (your limit) unless dispute.

### B) POD listings (Etsy + Printify)

1. **Trend scan** → generate clean, family-friendly designs.
2. **Printify create product** with selected **blueprint** + **provider**; place design to print areas.
3. **Publish to Etsy**; in Etsy, **add production partner (Printify)** and disclosure text. ([Etsy Help][12])
4. **Pricing:** use Printify base + shipping rates to compute a margin-safe CAD price; remember Etsy fees apply to item **and shipping**. ([Printify][13])
5. **Order handling:** leave fully automated (Printify auto-fulfills).

### C) Gumroad subscription

1. Create a monthly product (“Coding Practice Pack”).
2. Set **Ping webhook** to your worker URL; on sale/renewal → auto-email access link, tag CRM, update license table.
3. Keep **Discover OFF** initially to avoid 30% fee until LTV justifies it. ([Gumroad][3])

---

## 7) compliance & listing text

- **Etsy AI disclosure**: Include a short line in listings that used AI (“Design ideation assisted by AI; final assets curated and edited by us.”). ([Etsy][5])
- **Production partner**: Disclose Printify as production partner on all POD listings. ([Etsy Help][12])
- **Taxes (CA)**: track revenue vs **CA\$30k** small-supplier threshold; once exceeded, register and start charging/remitting GST/HST as required (Etsy’s marketplace collection rules vary by scenario; see Canada help page). ([Government of Canada][7])

---

## 8) monitoring & alerts (agent tasks)

- **Daily jobs:** failed API calls, listing activations, Printify publish errors; orders mismatched >15 minutes.
- **Weekly jobs:** price tests, keyword refresh, top 10 sellers duplication (new colorways).
- **Monthly jobs:** reply to messages, check Etsy policy updates, verify GST/HST threshold progress, evaluate Gumroad churn.

---

## 9) tool stack (keep costs tiny)

- **Must-have:** Etsy API app (free), Printify (free), lightweight DB, email sender (free tier).
- **Nice-to-have (only if ROI positive):** one low-cost design tool or LLM writer; n8n/Make free tier for webhooks.

---

## 10) delivery checklist (what the agent must hand back)

- Working repos for: `/etsy-digital`, `/printify-pod`, `/gumroad-subscription`.
- `.env.example` and README with scopes and callback URLs.
- One-click scripts: `create_digital_listing`, `create_pod_product`, `price_floor_calculator`.
- Unit-economics sheets (CSV) per SKU with computed margins using current rates.

---

## appendix — key links the agent relies on (for correctness)

- **Etsy fees & taxes (Canada):** listing/transaction/processing/currency conversion fees, **Regulatory 1.15% (CA)**, Offsite Ads **12%/15%**, setup fee note, and digital fulfillment. ([Etsy Help][4])
- **Etsy Canada tax handling & small supplier context** (marketplace collection & fee taxes). ([Etsy Help][1])
- **CRA small supplier rules (CA\$30k over 4 quarters).** ([Government of Canada][7])
- **Etsy API request standards + endpoints** (OAuth2, headers, create listings, upload files, activation behavior fix Jan 2025). ([developers.etsy.com][9])
- **Printify API** (create/publish products) & **shipping rates pages**. ([developers.printify.com][2])
- **Gumroad** pricing/MoR + **Ping** webhooks. ([Gumroad][3])
- **KDP** AI disclosure & royalty change (optional expansion). ([Kindle Direct Publishing][6])

---

## quick start (first 7 days)

**Day 1–2**

- Open Etsy shop (CAD currency), note any setup fee, connect Etsy API app; create 10 digital templates, stage listing JSON. ([Etsy Help][4])

**Day 3–4**

- Ship 20–30 digital listings live (API: draft → upload file → active). Turn **Offsite Ads OFF**.

**Day 5**

- Connect Printify via API, build 5 POD SKUs (Bella+Canvas 3001 tee is a good baseline), publish to Etsy; add **production partner**. ([developers.printify.com][2])

**Day 6**

- Create Gumroad product + webhook; set monthly plan at a low anchor (e.g., CA\$3.99–5.99) to seed subscribers. ([app.gumroad.com][11])

**Day 7**

- Validate unit-economics sheet; adjust prices to keep ≥70–85% margin on digital and ≥25–35% on POD after all Etsy fees & processing.

---

### final notes

- this plan deliberately **front-loads digital** (fastest to passive margin), then **layers POD** (scalable), then **adds recurring** (gumroad).
- policy/fee references above are current as of **Sept 12, 2025** and linked so the agent can re-check before actions.

if you want, i can generate the **exact API payloads** (sample JSON bodies) for your first 10 digital listings and 5 POD products so your agent can fire them off immediately.

[1]: https://help.etsy.com/hc/en-us/related/click?data=BAh7CjobZGVzdGluYXRpb25fYXJ0aWNsZV9pZGwrCBd8MnIIBjoYcmVmZXJyZXJfYXJ0aWNsZV9pZGwrCLKOoD9dAToLbG9jYWxlSSIKZW4tdXMGOgZFVDoIdXJsSSJWL2hjL2VuLXVzL2FydGljbGVzLzY2MzMzNDU0MTYyMTUtV2hhdC1Eb2VzLUV0c3ktUmVtaXQtdG8tQ2FuYWRpYW4tVGF4LUF1dGhvcml0aWVzBjsIVDoJcmFua2kJ--654921b54fd074afc0c7793b6f574ed6ecfdcbfa "What Does Etsy Remit to Canadian Tax Authorities? – Etsy Help"
[2]: https://developers.printify.com/ "Printify API Reference"
[3]: https://gumroad.com/pricing?utm_source=chatgpt.com "Gumroad pricing: 10% flat fee"
[4]: https://help.etsy.com/hc/en-us/articles/115014483627-What-are-the-Fees-and-Taxes-for-Selling-on-Etsy "What are the Fees and Taxes for Selling on Etsy? – Etsy Help"
[5]: https://www.etsy.com/legal/sellers/?utm_source=chatgpt.com "Seller Policy - Our House Rules"
[6]: https://kdp.amazon.com/help/topic/G200672390?utm_source=chatgpt.com "Content Guidelines - Kindle Direct Publishing"
[7]: https://www.canada.ca/en/revenue-agency/services/tax/businesses/topics/gst-hst-businesses/when-register-charge.html?utm_source=chatgpt.com "When to register for and start charging the GST/HST"
[8]: https://n8n.io/integrations/webhook/and/gumroad/?utm_source=chatgpt.com "Webhook and Gumroad integration"
[9]: https://developers.etsy.com/documentation/essentials/requests?utm_source=chatgpt.com "Request Standards | Etsy Open API v3"
[10]: https://developers.etsy.com/documentation/tutorials/quickstart?utm_source=chatgpt.com "Quick Start Tutorial | Etsy Open API v3"
[11]: https://app.gumroad.com/ping?utm_source=chatgpt.com "Gumroad Ping"
[12]: https://help.etsy.com/hc/en-us/articles/360000336547-Working-with-Production-Partners-on-Etsy?utm_source=chatgpt.com "Working with Production Partners on Etsy"
[13]: https://printify.com/shipping-rates/?utm_source=chatgpt.com "Shipping rates"

---

## selected stack & links (added)

- Automation glue: n8n (self‑host or cloud) for webhooks + cron; workers in Node/Python for Etsy/Printify heavy‑lifting. Pipedream acceptable alternative if you want hosted webhooks.
- AI models: Claude/OpenAI primary; Ollama‑hosted Llama/Qwen for batch ideation fallback. Structured JSON outputs with schema prompts.
- DB: SQLite → Postgres when scaling. Secrets via .env locally, secret manager later.

See detailed docs:

- docs/automation-tools.md
- docs/ai-stack.md
- docs/architecture.md
- docs/integration-plan.md
- docs/evaluation-matrix.md
- docs/roadmap.md
