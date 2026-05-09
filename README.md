# @devshub198211/devguard

> **One install. 14 modules. Zero external dependencies.**
> Security · AI Tooling · Auth · DX — everything a production Node.js project needs.

```bash
npm install @devshub198211/devguard
```

---

## Quick Start

```bash
# Initialize DevGuard in your project
npx @devshub198211/devguard init

# Run a full security audit
npx @devshub198211/devguard check

# AI-powered code refactor (free, runs locally)
npx @devshub198211/devguard refactor src/app.ts
```

For global CLI access:
```bash
npm install -g @devshub198211/devguard
devguard check
```

---

## All 14 Modules

| # | Category | Module | CLI Command | What It Does |
|---|----------|--------|-------------|--------------|
| 1 | 🔒 Security | `lockfile-guardian` | `check` / `snapshot` | SHA-512 lockfile tamper detection |
| 2 | 🔒 Security | `hook-scanner` | `scan` | 23-rule malware scanner for install scripts |
| 3 | 🔒 Security | `token-rotator` | `tokens` | Live API token verification + age alerts |
| 4 | 🔒 Security | `dep-pincer` | `pin --fix` | Enforce exact version pins + SRI hashes |
| 5 | 🤖 AI | `refactor-engine` | `refactor <file>` | Complexity analysis + security patching |
| 6 | 🤖 AI | `agent-schema` | `schema <json>` | Validate & auto-repair LLM JSON output |
| 7 | 🤖 AI | `mcp-server-kit` | `mcp` | Build Claude-compatible MCP tool servers |
| 8 | 🤖 AI | `agent-memory` | `memory --agent <id>` | Durable state for AI agents (FS/Redis) |
| 9 | 🤖 AI | `llm-budget` | `budget` | Token counting + cost tracking |
| 10 | 🔑 Auth | `zero-trust-jwt` | `jwt-verify` / `jwt-sign` | JWT sign & verify with anomaly detection |
| 11 | 🔑 Auth | `bot-fence` | `bot-check --ip <ip>` | Multi-signal bot detection middleware |
| 12 | 🔑 Auth | `passkey-node` | `passkey-verify` | WebAuthn passkey registration & auth |
| 13 | 🛠 DX | `env-safe` | `env-verify` | Typed .env validation — fail fast |
| 14 | 🛠 DX | `log-otlp` | `log --msg <text>` | Structured JSON logger + OpenTelemetry |

**Bonus:** `api-contract` — zero-dep schema builder with TypeScript inference.

---

## Use From Any Language (Python, Go, Bash, etc.)

Every command supports `--json` output, making DevGuard usable from **any programming language**:

### Python
```python
import subprocess, json

result = subprocess.run(
    ["npx", "@devshub198211/devguard", "check", "--json"],
    capture_output=True, text=True
)
report = json.loads(result.stdout)
print(f"Security Score: {report['score']}/100")
```

### Go
```go
cmd := exec.Command("npx", "@devshub198211/devguard", "scan", "--json")
output, _ := cmd.Output()
// Parse JSON output
```

### Bash
```bash
SCORE=$(npx @devshub198211/devguard check --json | jq '.score')
echo "Score: $SCORE"

# Sign a JWT
TOKEN=$(devguard jwt-sign --payload '{"sub":"user-1"}' --secret mykey --json | jq -r '.token')

# Check an IP
devguard bot-check --ip 203.0.113.5 --json | jq '.verdict'
```

### Ruby
```ruby
result = `npx @devshub198211/devguard check --json`
report = JSON.parse(result)
puts "Score: #{report['score']}"
```

---

## CLI Reference

```bash
# Core
devguard init                                    # Set up project
devguard check [--json]                          # Full security audit
devguard refactor <file> [--json]                # AI code refactor
devguard mcp                                     # Start MCP server

# Security
devguard snapshot                                # Create lockfile baseline
devguard scan [--json]                           # Malware scan
devguard tokens [--json]                         # Token health check
devguard pin [--fix] [--json]                    # Pin dependencies

# AI
devguard schema '<json>' [--json]                # Validate JSON
devguard memory [--agent <id>] [--json]          # Query agent state
devguard budget [--json]                         # LLM cost summary

# Auth
devguard jwt-verify --token <t> --secret <s>     # Verify JWT
devguard jwt-sign --payload '<json>' --secret <s> # Sign JWT
devguard bot-check --ip <ip> [--json]            # Bot detection

# DX
devguard env-verify [--file .env] [--json]       # Validate env
devguard log --msg <text> [--level info|warn|error]  # Emit log
```

---

## Programmatic Usage (Node.js / TypeScript)

```typescript
import { runAllChecks } from '@devshub198211/devguard';

const report = await runAllChecks();
console.log(`Security Score: ${report.score}/100`);
if (!report.passedAll) process.exit(1);
```

### Tree-Shakeable Sub-Path Imports

```typescript
import { verifyLockfile } from '@devshub198211/devguard/security';
import { LLMBudget }      from '@devshub198211/devguard/ai';
import { JWTVerifier }    from '@devshub198211/devguard/auth';
import { loadEnv }        from '@devshub198211/devguard/dx';
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
      - run: npx @devshub198211/devguard check --json > report.json
      - run: node -e "const r=JSON.parse(require('fs').readFileSync('report.json'));if(!r.passedAll)process.exit(1)"
```

---

## Security Guarantees

- ✅ **Zero external runtime dependencies** — only Node.js built-ins
- ✅ **No network calls** at runtime (except opt-in token verification)
- ✅ **No telemetry, no tracking, no phone-home**
- ✅ **Constant-time JWT comparison** — timing attack protection
- ✅ **Sign-count replay protection** in WebAuthn
- ✅ **Path traversal protection** — all file IDs are SHA-256 hashed
- ✅ **Prototype pollution prevention** in CBOR/JSON parsers
- ✅ **Atomic file writes** — no data corruption during crashes
- ✅ **Works fully offline** — all security checks are local

---

## Requirements

- Node.js >= 18.0.0

## License

MIT © DevGuard Contributors

---

*Your custom notes below:*

<!-- Add your personal notes, contact info, or acknowledgments here -->
