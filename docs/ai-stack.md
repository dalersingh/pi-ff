# AI Stack (Models, Agents, and Prompts)

Objectives

- Generate evergreen digital assets (templates, planners, kits) safely.
- Assist with POD ideation and copy (titles, tags, descriptions) within marketplace policies.
- Keep costs low; avoid lock-in; support offline or API fallback.

Model providers (2025 landscape)

- OpenAI (GPT‑4o family, o3 for reasoning): strong general performance; paid API; good for copy and structured JSON.
- Anthropic (Claude 3.7/3.5): safe long-context writing; great at instructions; competitive pricing.
- Google (Gemini 1.5): strong multimodal/context; variable reliability per endpoint.
- **Google (Gemini 2.5 Flash Image / "Nano Banana")**: Native image generation with text prompts; excellent for POD design automation; REST API integration.
- Open‑source (Llama 3.x/4, Qwen2.5): run via Ollama or cloud-hosted (Together, Groq). Great for cost control and local/offline.

Recommendation for this project

- Primary: Anthropic Claude or OpenAI for copy generation and structured SKU metadata due to stability and JSON control.
- **Image Generation**: Gemini 2.5 Flash Image for automated design creation (text-to-image, design variations, product mockups).
- Secondary: Ollama‑hosted Llama/Qwen for batch ideation when cost sensitive.
- JSON mode/Schema: use a JSON schema prompt pattern to emit exact fields (title, 13 tags, description ≤ 140 chars for Etsy, etc.).

Agent and orchestration options

- Keep it simple: tool‑using functions that call workers (no heavy autonomous agents needed).
- Options if desired: LangChain (tools + structured output), Guidance or JSON schema prompts, simple function‑calling wrappers.
- Avoid long‑running agents; prefer stateless steps driven by the scheduler/webhook.

Safety & policy compliance

- Family‑friendly content; ban restricted topics per Etsy/Gumroad rules.
- Include AI‑disclosure lines in listings; manual review sampling on 5–10% of outputs.
- Keep a blocklist of prohibited terms; run a final lint pass.
- **Image Generation**: All Gemini-generated images include SynthID watermarks for transparency.

Enhanced workflow with image generation

1. **Design Generation**: Gemini 2.5 Flash Image → create product designs based on trending keywords
2. **Copy Generation**: Claude/OpenAI → generate titles, tags, descriptions
3. **Asset Upload**: n8n workflow → upload images to Printify, create products
4. **Listing Creation**: Automated → publish to Etsy with AI disclosure

Prompt contract (example)
Inputs

- product_type (digital|pod), niche, audience, marketplace rules, character limits, **design_brief** (for image generation)
  Outputs (JSON)
- title (≤ 140)
- description (≤ 1000, policy‑safe)
- **image_prompt** (for Gemini 2.5 Flash Image)
- **design_variations** (array of alternative prompts)
- tags (array of ≤ 13, 1–20 chars)
- materials (POD), personalization prompt (optional)
- seo_keywords (≤ 10)

Cost control

- Use small models for ideation, larger for final polish.
- Batch prompts; stream results into CSV for review before publish.

Minimal stack

- Node/Python worker with a provider SDK (OpenAI/Anthropic) + fallback to Ollama.
- Retry with circuit breaker; redact secrets in logs.
