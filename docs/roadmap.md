# Roadmap (90 days)

## Critical Gap Assessment + Foundation (Week 0-1) **UPDATED**

- **CRITICAL**: Address mandatory Google Cloud billing tier requirement
- **CRITICAL**: Legal consultation on AI content liability and IP protection
- **CRITICAL**: Update cost model and break-even calculations
- **CRITICAL**: Design human-in-the-loop quality review system
- Implement multi-tier rate limiting queues
- Add webhook signature verification
- Build basic quota monitoring dashboard
- Create error handling with retry logic
- **NEW**: Deploy Gemini 2.5 Flash Image generation pipeline (Tier 1+ only)
- **NEW**: Integrate complete design-to-sale automation workflowWeeks 1–2

- Stand up n8n (self-host or cloud). Implement Gumroad Ping intake.
- **UPDATED**: Deploy AI-powered POD pipeline with Gemini image generation (requires Google Cloud billing)
- **UPDATED**: Implement quality control workflow for AI-generated designs
- Add monitoring and daily cron. Price floor calculator MVP.
- **Enhanced**: Deploy circuit breaker patterns for API calls
- **NEW**: Launch controlled AI-powered POD pipeline with human review queue (5-10 SKUs initially)

Weeks 3–4

- **UPDATED**: Scale AI-generated POD pipeline after quality validation (10-20 SKUs)
- **CRITICAL**: Monitor Google Cloud billing tier progression and rate limits
- Add Printify POD pipeline baseline. Shipping/base-cost fetch + margin rules. Unit-economics CSV per SKU.
- **Enhanced**: Implement webhook delivery confirmation tracking
- **NEW**: Implement brand consistency monitoring and style guide enforcement

Weeks 5–8

- **UPDATED**: Full AI automation deployment with legal safeguards in place
- Subscription MVP on Gumroad; onboarding email; churn tracking. AB test thumbnails and tags weekly.
- **Enhanced**: Add progressive backoff for rate limit violations
- **NEW**: Deploy market differentiation strategy to combat AI POD saturation

Weeks 9–12

- **UPDATED**: Optimize AI generation costs and monitor tier progression to Tier 2 ($250+ spend)
- Automate duplication of top sellers; expand SKUs. Evaluate moving to Postgres. Consider Slack alerting, error dashboards.
- **Enhanced**: Optimize with batch operations and comprehensive monitoring
- **NEW**: Implement seasonal scaling strategy and pre-generation workflows for peak periods

Stretch

- Optional KDP low-content; ensure AI disclosure + pricing calculator.
- **New**: Manual fallback procedures and business continuity planning
- **New**: Advanced AI image generation with style consistency and brand voice automation
- **New**: Multi-marketplace expansion (Amazon, Redbubble) using same AI generation pipeline
