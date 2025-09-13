# Implementation Guide: Enhanced POD Automation with AI Image Generation

_Concrete steps to implement the critical fixes identified in gap-analysis.md plus new Gemini 2.5 Flash Image integration for complete design-to-sale automation_

## Priority 0: AI Image Generation Integration (NEW)

### n8n Workflow: Gemini 2.5 Flash Image Pipeline

```javascript
// Gemini Image Generation Node
const imagePrompt = $input.first().json.design_brief;
const geminiPayload = {
  contents: [
    {
      parts: [
        {
          text: `Create a professional print-on-demand design: ${imagePrompt}. 
             Style: Clean, modern, commercial quality. 
             Format: High resolution suitable for t-shirts, mugs, posters.
             No text overlays, focus on visual elements only.`,
        },
      ],
    },
  ],
};

const options = {
  method: "POST",
  url: "https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-image-preview:generateContent",
  headers: {
    "x-goog-api-key": process.env.GEMINI_API_KEY,
    "Content-Type": "application/json",
  },
  body: JSON.stringify(geminiPayload),
};

const response = await this.helpers.httpRequest(options);
const imageData = response.candidates[0].content.parts[0].inline_data.data;

return [
  {
    json: {
      design_prompt: imagePrompt,
      image_base64: imageData,
      image_format: "png",
      generated_at: new Date().toISOString(),
    },
  },
];
```

### Enhanced Design-to-Sale Workflow

```javascript
// Complete automation pipeline
async function executeDesignToSaleWorkflow(keywords) {
  const steps = [
    "1. Keyword Analysis & Design Brief Generation",
    "2. Gemini 2.5 Flash Image Generation",
    "3. Claude Copy Generation (title, tags, description)",
    "4. Image Upload to Printify",
    "5. Product Creation via Printify API",
    "6. Etsy Listing Publication with AI Disclosure",
    "7. Performance Tracking & Monitoring",
  ];

  // Step 1: Generate design brief
  const designBrief = await generateDesignBrief(keywords);

  // Step 2: Create images with Gemini
  const images = await geminiImageGeneration(designBrief);

  // Step 3: Generate copy with Claude
  const copyContent = await claudeCopyGeneration(keywords, designBrief);

  // Step 4-7: Execute remaining automation steps
  return await executePrintifyEtsyFlow(images, copyContent);
}
```

### Implementation Steps:

1. Add Gemini API key to .env configuration
2. Create image generation node in n8n workflows
3. Implement base64 to file conversion for Printify uploads
4. Add error handling for API failures and retries
5. Create monitoring for generation success rates

## Priority 1: Rate Limit Queue Management

### n8n Workflow: Rate Limit Manager

```javascript
// Custom Code Node - Rate Limit Check
const queueLimits = {
  printify_global: { limit: 500, window: 60 },
  printify_catalog: { limit: 80, window: 60 },
  printify_publish: { limit: 150, window: 1800 }, // 30 min
  etsy_main: { limit: 8, window: 1 },
};

const queueType = $input.first().json.queue_type;
const currentCount = await getQueueCount(queueType);

if (currentCount >= queueLimits[queueType].limit) {
  return [
    {
      json: {
        action: "delay",
        delay_ms: queueLimits[queueType].window * 1000,
      },
    },
  ];
}

return [{ json: { action: "proceed" } }];
```

### Implementation Steps:

1. Create Redis/SQLite queue counter tables
2. Add rate limit check node before each API call
3. Implement progressive delay logic
4. Add quota monitoring alerts
5. **NEW**: Include Gemini API rate limits in queue management

## Priority 2: Webhook Signature Verification

### n8n Webhook Security Node

```javascript
// Webhook Signature Verification
const crypto = require("crypto");

function verifyPrintifySignature(payload, signature, secret) {
  const expectedSignature = crypto
    .createHmac("sha256", secret)
    .update(payload)
    .digest("hex");

  return signature === `sha256=${expectedSignature}`;
}

const payload = JSON.stringify($input.first().json);
const signature = $input.first().headers["x-pfy-signature"];
const secret = process.env.PRINTIFY_WEBHOOK_SECRET;

if (!verifyPrintifySignature(payload, signature, secret)) {
  throw new Error("Invalid webhook signature");
}

return [$input.first()];
```

### Implementation Steps:

1. Add webhook secret to .env
2. Create signature verification node
3. Place before webhook processing logic
4. Add logging for failed verifications

## Priority 3: Circuit Breaker Pattern

### n8n Circuit Breaker Implementation

```javascript
// Circuit Breaker State Management
const circuitBreakerStates = {
  printify: { failures: 0, lastFailure: null, state: "closed" },
  etsy: { failures: 0, lastFailure: null, state: "closed" },
  gumroad: { failures: 0, lastFailure: null, state: "closed" },
};

const FAILURE_THRESHOLD = 5;
const RECOVERY_TIMEOUT = 300000; // 5 minutes

function checkCircuitBreaker(service) {
  const state = circuitBreakerStates[service];

  if (state.state === "open") {
    if (Date.now() - state.lastFailure > RECOVERY_TIMEOUT) {
      state.state = "half-open";
      return { allow: true, state: "half-open" };
    }
    return { allow: false, state: "open" };
  }

  return { allow: true, state: state.state };
}

function recordSuccess(service) {
  circuitBreakerStates[service].failures = 0;
  circuitBreakerStates[service].state = "closed";
}

function recordFailure(service) {
  const state = circuitBreakerStates[service];
  state.failures++;
  state.lastFailure = Date.now();

  if (state.failures >= FAILURE_THRESHOLD) {
    state.state = "open";
  }
}
```

### Implementation Steps:

1. Add circuit breaker state tracking
2. Wrap API calls with breaker logic
3. Implement fallback procedures
4. Add manual reset capabilities

## Priority 4: Enhanced Error Handling

### n8n Error Handler Workflow

```javascript
// Enhanced Error Handling
function handleAPIError(error, service, operation) {
  const errorHandling = {
    429: {
      // Rate limit
      action: "retry",
      delay: error.headers?.["retry-after"] * 1000 || 60000,
      maxRetries: 3,
    },
    500: {
      // Server error
      action: "retry",
      delay: 5000,
      maxRetries: 5,
    },
    401: {
      // Auth error
      action: "alert",
      priority: "high",
      message: `Authentication failed for ${service}`,
    },
    403: {
      // Forbidden
      action: "alert",
      priority: "medium",
      message: `Permission denied for ${service}:${operation}`,
    },
  };

  const handling = errorHandling[error.status] || {
    action: "log",
    priority: "low",
  };

  return handling;
}
```

## Priority 5: Monitoring Dashboard Setup

### Basic Monitoring Queries

```sql
-- Rate limit quota tracking
CREATE TABLE api_quota_usage (
  service VARCHAR(50),
  endpoint VARCHAR(100),
  requests_used INTEGER,
  quota_limit INTEGER,
  window_start TIMESTAMP,
  PRIMARY KEY (service, endpoint, window_start)
);

-- Webhook delivery tracking
CREATE TABLE webhook_deliveries (
  id UUID PRIMARY KEY,
  service VARCHAR(50),
  event_type VARCHAR(100),
  delivered_at TIMESTAMP,
  retry_count INTEGER DEFAULT 0,
  success BOOLEAN
);

-- Circuit breaker events
CREATE TABLE circuit_breaker_events (
  service VARCHAR(50),
  state VARCHAR(20), -- open, closed, half-open
  changed_at TIMESTAMP,
  failure_count INTEGER
);
```

### n8n Monitoring Workflow

```javascript
// Daily Quota Report
const services = ["etsy", "printify", "gumroad"];
const report = [];

for (const service of services) {
  const usage = await getQuotaUsage(service);
  const percentage = (usage.used / usage.limit) * 100;

  report.push({
    service,
    usage_percentage: percentage,
    requests_used: usage.used,
    quota_limit: usage.limit,
    status: percentage > 80 ? "warning" : "ok",
  });
}

// Send alert if any service > 80%
const alerts = report.filter((r) => r.status === "warning");
if (alerts.length > 0) {
  await sendSlackAlert(
    `Quota warning: ${alerts.map((a) => a.service).join(", ")}`
  );
}
```

## Implementation Timeline

### Week 0 (Immediate - Enhanced)

- [ ] Add rate limit environment variables
- [ ] **NEW**: Add Gemini API key and image generation endpoints
- [ ] Create basic queue counter system
- [ ] Implement webhook signature verification
- [ ] Add error handling wrapper
- [ ] **NEW**: Build Gemini image generation workflow in n8n

### Week 1 (Core Reliability + AI Integration)

- [ ] Deploy circuit breaker patterns
- [ ] Create monitoring database tables
- [ ] Build quota tracking system
- [ ] Add Slack alerting integration
- [ ] **NEW**: Implement complete design-to-sale automation pipeline
- [ ] **NEW**: Add AI disclosure text generation for Etsy listings

### Week 2 (Optimization + Enhanced Monitoring)

- [ ] Implement progressive backoff
- [ ] Create monitoring dashboard
- [ ] Add manual override procedures
- [ ] Performance testing and tuning
- [ ] **NEW**: Add image generation performance metrics
- [ ] **NEW**: Implement A/B testing for AI-generated designs

## Testing Strategy

### Rate Limit Testing

```bash
# Test Printify rate limits
for i in {1..605}; do
  curl -H "Authorization: Bearer $PRINTIFY_TOKEN" \
       "https://api.printify.com/v1/shops.json" &
done
wait
```

### Webhook Testing

```bash
# Test webhook signature verification
payload='{"test": "data"}'
signature=$(echo -n "$payload" | openssl dgst -sha256 -hmac "$WEBHOOK_SECRET" | cut -d' ' -f2)
curl -X POST "$WEBHOOK_URL" \
     -H "Content-Type: application/json" \
     -H "X-Pfy-Signature: sha256=$signature" \
     -d "$payload"
```

### Circuit Breaker Testing

```javascript
// Simulate API failures
async function testCircuitBreaker() {
  for (let i = 0; i < 10; i++) {
    try {
      await makeAPICall("failing-endpoint");
    } catch (error) {
      console.log(`Attempt ${i + 1}: ${error.message}`);
    }
  }
}
```

## Monitoring Alerts

### Critical Alerts (Immediate Response)

- Rate limit quota > 90%
- Circuit breaker opened
- Webhook delivery failure rate > 10%
- Authentication failures

### Warning Alerts (Review Within Hours)

- Rate limit quota > 80%
- API response time > 5 seconds
- Error rate > 5%
- Queue backlog > 100 items

### Info Alerts (Daily Review)

- Daily usage summaries
- Performance metrics
- Success rate reports

---

_Implement these components incrementally. Start with rate limiting and webhook security as they have the highest impact on reliability._
