# DevGuard Modules Reference

All 14 modules explained with examples.

---

## Security Modules

### 1. lockfile-guardian
**What:** Detects tampering in `package-lock.json`, `yarn.lock`, or `pnpm-lock.yaml` using SHA-512 hashing.

```typescript
import { verifyLockfile, createSnapshot } from '@devshub198211/devguard/security';

// Create a trusted baseline after clean install
createSnapshot();

// Later, verify nothing has changed
const result = verifyLockfile();
if (!result.valid) console.log('Tampered files:', result.tampered);
```

```bash
devguard snapshot   # Create baseline
devguard check      # Verify integrity
```

---

### 2. hook-scanner
**What:** Scans `node_modules` for 23+ malicious patterns in install scripts (obfuscation, network exfiltration, shell commands).

```typescript
import { scanProject } from '@devshub198211/devguard/security';

const findings = scanProject();
findings.forEach(f => console.log(`${f.package}: ${f.pattern} [${f.severity}]`));
```

```bash
devguard scan
```

---

### 3. token-rotator
**What:** Checks API token age and verifies tokens are still valid via live API calls (opt-in).

```typescript
import { checkTokenAge, loadTokensFromEnv } from '@devshub198211/devguard/security';

const tokens = loadTokensFromEnv(['NPM_TOKEN', 'GITHUB_TOKEN']);
const alerts = checkTokenAge(tokens);
alerts.filter(a => a.status === 'stale').forEach(a => console.log(`Rotate: ${a.name}`));
```

```bash
devguard tokens
```

---

### 4. dep-pincer
**What:** Finds dependencies using ranges (`^1.0.0`) and pins them to exact versions. Generates SRI hashes.

```typescript
import { autoPin } from '@devshub198211/devguard/security';

const result = autoPin('.', true); // true = auto-fix
console.log(`Pinned ${result.fixed} dependencies`);
```

```bash
devguard pin --fix
```

---

## AI & Code Modules

### 5. refactor-engine
**What:** Analyzes code for complexity (O(n) vs O(n²)), security flaws (eval, innerHTML), and structural issues. Opens a browser-based diff reviewer.

```bash
devguard refactor src/utils.ts
```

Detects and fixes:
- `eval()` → `JSON.parse()` (security)
- `.innerHTML` → `.textContent` (XSS prevention)
- `var` → `const` (modern JS)
- Deep nesting → guard clauses (readability)
- Missing try/catch in async functions (error safety)

---

### 6. agent-schema
**What:** Type-safe schema builder for validating LLM JSON output with auto-retry and structural repair.

```typescript
import { c, parseWithRetry } from '@devshub198211/devguard/ai';

const TaskSchema = c.object({
  title:    c.string().min(1),
  priority: c.string().enum(['high', 'medium', 'low']),
});

const result = await parseWithRetry(TaskSchema, async (ctx) => {
  return await callYourLLM(ctx);
});
// result.data is fully typed: { title: string; priority: 'high'|'medium'|'low' }
```

---

### 7. mcp-server-kit
**What:** Build Model Context Protocol servers that work with Claude Desktop and other MCP clients.

```typescript
import { MCPServerBuilder } from '@devshub198211/devguard/ai';

new MCPServerBuilder('my-tools', '1.0.0')
  .addTool({
    name: 'get_weather',
    description: 'Get weather for a city',
    inputSchema: { type: 'object', properties: { city: { type: 'string' } }, required: ['city'] },
    handler: async ({ city }) => ({ temp: '22°C', city })
  })
  .startStdio();
```

```bash
devguard mcp
```

---

### 8. agent-memory
**What:** Persistent state storage for AI agents. Supports FileSystem and in-memory adapters.

```typescript
import { FileSystemAdapter } from '@devshub198211/devguard/ai';

const memory = new FileSystemAdapter('.agent-data');
await memory.save('agent-1', {
  id: '1', role: 'user', content: 'Hello', timestamp: Date.now()
});
const history = await memory.getHistory('agent-1', 10);
```

---

### 9. llm-budget
**What:** Track token usage and spending across OpenAI, Anthropic, Google, and other LLM providers.

```typescript
import { LLMBudget } from '@devshub198211/devguard/ai';

const budget = new LLMBudget({ monthlyLimitUSD: 50, warnAtUSD: 40 });
budget.track({
  model: 'gpt-4o', provider: 'openai',
  tokensPrompt: 1000, tokensCompletion: 500, costUSD: 0.015
});
console.log(budget.report()); // { totalCost, isOverBudget, ... }
```

---

## Auth Modules

### 10. zero-trust-jwt
**What:** JWT verification with HS256/RS256/JWKS support, revocation lists, and anomaly detection.

```typescript
import { JWTVerifier } from '@devshub198211/devguard/auth';

const verifier = new JWTVerifier({ secret: process.env.JWT_SECRET });
const { valid, payload, anomalies } = await verifier.verify(token);
// anomalies: { score: 0, level: 'safe', warnings: [] }
```

```bash
devguard jwt-verify --token <jwt> --secret <key>
```

---

### 11. bot-fence
**What:** Multi-signal bot detection for Express/Fastify middleware. Checks IP, headers, rate limits.

```typescript
import { createMiddleware, IPRateLimiter } from '@devshub198211/devguard/auth';

app.use(createMiddleware({
  blockThreshold: 70,
  rateLimiter: new IPRateLimiter(100, 60_000)
}));
```

```bash
devguard bot-check
```

---

### 12. passkey-node
**What:** Complete WebAuthn/Passkey implementation using only Node.js crypto. Supports ES256 and RS256.

```typescript
import {
  generateRegistrationOptions,
  verifyRegistration,
  generateAuthenticationOptions,
  verifyAuthentication
} from '@devshub198211/devguard/auth';

const options = generateRegistrationOptions({
  rpId: 'example.com', rpName: 'My App',
  userId: 'user-1', userName: 'alice'
});
```

---

## DX Modules

### 13. env-safe
**What:** Validates `.env` files against a typed schema at startup. Fails fast with clear error messages.

```typescript
import { loadEnv } from '@devshub198211/devguard/dx';

const env = loadEnv({
  DATABASE_URL: { type: 'url',     required: true },
  PORT:         { type: 'integer', default: '3000', min: 1, max: 65535 },
  NODE_ENV:     { type: 'string',  enum: ['development', 'production', 'test'] },
});
// Throws immediately if any variable is missing or invalid
```

---

### 14. log-otlp
**What:** Structured JSON logger compatible with OpenTelemetry. Supports child loggers and trace injection.

```typescript
import { createLogger } from '@devshub198211/devguard/dx';

const log = createLogger({ service: 'api', level: 'info' });
log.info('Request received', { userId: 'u-123', path: '/orders' });
log.error('Payment failed', { orderId: 'o-456', reason: 'declined' });

const reqLog = log.child({ requestId: 'req-789' });
reqLog.info('Processing');
```

---

### Bonus: api-contract
**What:** Zero-dependency schema builder with full TypeScript type inference for API validation.

```typescript
import { c, validateBody } from '@devshub198211/devguard/dx';

const UserSchema = c.object({
  name:  c.string().min(1).max(100),
  email: c.string(),
  age:   c.number().min(0).max(150).optional(),
});

// Express middleware
app.post('/users', validateBody(UserSchema), (req, res) => {
  // req.body is fully typed
});
```

---

*Your custom notes below:*

<!-- Add your personal notes, contact info, or acknowledgments here -->
