# devguard

> **One install. 14 features. Zero external dependencies.**  
> Security · AI Tooling · Auth · DX — everything a production Node.js/TypeScript project needs.

```bash
npm install devguard
npx devguard        # instant security scan — no install needed
```

[![npm](https://img.shields.io/npm/v/devguard)](https://www.npmjs.com/package/devguard)
[![license](https://img.shields.io/npm/l/devguard)](LICENSE)
[![node](https://img.shields.io/node/v/devguard)](package.json)

---

## Features

| Category | Feature | What it does |
|----------|---------|-------------|
| 🔒 Security | `lockfile-guardian` | SHA-512 tamper detection for npm/yarn/pnpm lockfiles |
| 🔒 Security | `hook-scanner` | 23-rule malware scanner for install scripts (obfuscation-aware) |
| 🔒 Security | `token-rotator` | Live API verification + age alerts for npm/GitHub tokens |
| 🔒 Security | `dep-pincer` | Enforce exact version pins + SRI hash verification |
| 🤖 AI | `agent-schema` | Validate LLM JSON output, auto-retry on malformed responses |
| 🤖 AI | `mcp-server-kit` | Build Claude-compatible MCP tool servers in minutes |
| 🤖 AI | `agent-memory` | Durable agent state: Memory / FileSystem / Redis / DynamoDB |
| 🤖 AI | `llm-budget` | Token counting + cost tracking for OpenAI/Anthropic/Gemini |
| 🔑 Auth | `zero-trust-jwt` | JWT verify (HS256/RS256/JWKS), revocation, anomaly detection |
| 🔑 Auth | `bot-fence` | Multi-signal bot detection middleware for Express/Fastify |
| 🔑 Auth | `passkey-node` | Production WebAuthn passkey registration & authentication |
| 🛠 DX | `env-safe` | Typed .env validation with built-in parser — fail fast |
| 🛠 DX | `log-otlp` | Structured JSON logger with OpenTelemetry trace injection |
| 🛠 DX | `api-contract` | Zero-dep schema builder with full TypeScript type inference |

---

## Quick Start

### CLI
```bash
npx devguard                    # full security scan + score
npx devguard lockfile snapshot  # create baseline after clean install
npx devguard lockfile verify    # check integrity
npx devguard hooks              # scan node_modules for malware
npx devguard pins --fix         # auto-pin unpinned dependencies
npx devguard tokens --live      # verify tokens via API
npx devguard --json             # machine-readable output for CI
```

### Programmatic
```typescript
import { runAllChecks } from 'devguard';

const report = await runAllChecks();
// { score: 94, passedAll: true, lockfile: {...}, hooks: {...}, ... }
if (!report.passedAll) process.exit(1);
```

### Tree-shakeable sub-path imports
```typescript
import { verifyLockfile } from 'devguard/security';
import { LLMBudget }      from 'devguard/ai';
import { JWTVerifier }    from 'devguard/auth';
import { loadEnv }        from 'devguard/dx';
```

---

## Examples

### Security — run all checks
```typescript
import { runAllChecks } from 'devguard';
const report = await runAllChecks();
console.log(`Security score: ${report.score}/100`);
```

### AI — validate LLM output
```typescript
import { c, parseWithRetry } from 'devguard';

const TaskSchema = c.object({
  title:    c.string().min(1),
  priority: c.string().enum(["high","medium","low"]),
  dueDate:  c.string().optional(),
});

const result = await parseWithRetry(TaskSchema, async (ctx) => {
  return await callYourLLM(ctx); // your LLM call here
});
// result.data is fully typed: { title: string; priority: "high"|"medium"|"low"; dueDate?: string }
```

### AI — agent with durable memory
```typescript
import { createMemory } from 'devguard';

// Persists across serverless invocations
const memory = createMemory({ agentId: 'agent-001', adapter: 'fs', ttl: 7200 });
await memory.setState({ step: 3, context: 'processing order' });
await memory.appendHistory('user', 'Cancel my order');
const history = await memory.getHistory(10); // last 10 messages
```

### AI — build an MCP tool server
```typescript
import { MCPServerBuilder } from 'devguard';

new MCPServerBuilder('my-tools', '1.0.0')
  .addTool({
    name: 'get_weather',
    description: 'Get weather for a city',
    inputSchema: { type:'object', properties:{ city:{type:'string'} }, required:['city'] },
    handler: async ({ city }) => ({ temp: '22°C', city })
  })
  .startStdio(); // works with Claude Desktop + any MCP client
```

### Auth — JWT verification
```typescript
import { JWTVerifier } from 'devguard';

const verifier = new JWTVerifier({ secret: process.env.JWT_SECRET! });
const { valid, payload, anomalies } = await verifier.verify(token);
// anomalies: { score: 0, level: 'safe', warnings: [] }
```

### Auth — bot detection middleware
```typescript
import express from 'express';
import { createMiddleware, IPRateLimiter } from 'devguard';

const app = express();
app.use(createMiddleware({
  blockThreshold: 70,
  rateLimiter: new IPRateLimiter(100, 60_000)
}));
```

### DX — typed .env validation
```typescript
import { loadEnv } from 'devguard';

const env = loadEnv({
  DATABASE_URL: { type: 'url',     required: true },
  PORT:         { type: 'integer', default: '3000', min: 1, max: 65535 },
  NODE_ENV:     { type: 'string',  enum: ['development','production','test'] },
  API_KEY:      { type: 'string',  required: true, minLength: 32, secret: true },
});
// Throws with clear message if invalid. env.PORT is typed as number.
```

### DX — structured logging
```typescript
import { createLogger } from 'devguard';

const log = createLogger({ service: 'api', level: 'info' });
log.info('Request received', { userId: 'u-123', path: '/orders' });
log.error('Payment failed', { orderId: 'o-456', reason: 'declined' });

// Child logger inherits bindings
const reqLog = log.child({ requestId: 'req-789' });
reqLog.info('Processing');
```

---

## CI/CD Integration

```yaml
name: DevGuard Security
on: [push, pull_request]
jobs:
  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: '20' }
      - run: npm ci
      - run: npx devguard --json > devguard-report.json
        env:
          DEVGUARD_TOKENS: NPM_TOKEN,GITHUB_TOKEN
          NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      - uses: actions/upload-artifact@v4
        with: { name: security-report, path: devguard-report.json }
      - run: node -e "const r=require('./devguard-report.json'); if(!r.passedAll) process.exit(1)"
```

---

## Security

- ✅ **Zero external runtime dependencies** — only Node.js built-ins
- ✅ **No network calls** at runtime except token inspection (opt-in)
- ✅ **No telemetry, no tracking, no phone-home**
- ✅ **Constant-time JWT comparison** — prevents timing attacks
- ✅ **Sign-count replay protection** in WebAuthn
- ✅ **Works fully offline** — all security checks are local

---

## License

MIT © DevGuard Contributors
