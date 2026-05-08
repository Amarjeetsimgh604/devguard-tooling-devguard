# devguard — Complete Setup, Publish & Monetisation Guide

## PART 1: Local Setup

### Step 1 — Prerequisites
```
node --version   # must be >= 18.0.0
npm --version    # must be >= 9.0.0
```

### Step 2 — Unzip & Install
```
unzip devguard-final.zip
cd devguard-final
npm install
```

### Step 3 — Build
```
npm run build
```
Generates dist/ with CJS + ESM + TypeScript declarations + CLI.

### Step 4 — Test CLI locally
```
node dist/cli.js                    # full security scan
node dist/cli.js lockfile snapshot  # create integrity baseline
node dist/cli.js lockfile verify    # verify against baseline
node dist/cli.js hooks              # scan for malicious scripts
node dist/cli.js pins --fix         # auto-fix unpinned deps
node dist/cli.js tokens --live      # live API token check
node dist/cli.js --json             # machine-readable output
node dist/cli.js help               # all commands
```

### Step 5 — Use in your project
```
npm install devguard
```

```typescript
import { runAllChecks } from 'devguard';
const report = await runAllChecks();
console.log(report.score); // 0-100
if (!report.passedAll) process.exit(1);
```

---

## PART 2: Publish to npm

### Step 1 — Create Account
1. https://www.npmjs.com/signup
2. Verify email
3. Enable 2FA (mandatory): https://www.npmjs.com/settings/~/profile

### Step 2 — Login
```
npm login
npm whoami
```

### Step 3 — Check name availability
```
npm info devguard
```
If taken, rename in package.json: "name": "@yourscope/devguard"

### Step 4 — Dry run
```
npm publish --dry-run --access public
```

### Step 5 — Publish
```
npm run build
npm publish --access public
```

### Step 6 — Verify
```
npm info devguard
npx devguard help
```

### Step 7 — Update versions
```
npm version patch    # 2.0.0 -> 2.0.1
npm run build
npm publish --access public
```

---

## PART 3: GitHub Setup

```
git init
git add .
git commit -m "feat: devguard v2.0.0"
gh repo create devguard --public --push
```

Add CI (.github/workflows/ci.yml):
```yaml
name: CI
on: [push, pull_request]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: '20', cache: 'npm' }
      - run: npm ci && npm run build
      - run: node dist/cli.js --json
```

---

## PART 4: Monetisation

### Free (builds audience)
- GitHub Sponsors: github.com/sponsors/onboarding — $5/$15/$50 tiers
- Polar.sh: polar.sh — connect repo, create paid issues
- Target: $200-2000/month at 1k+ weekly downloads

### Pro SaaS ($19-299/month)
Create @devguard/pro package with:
- Web dashboard (Next.js + Stripe + Supabase)
- Slack/email alerts for stale tokens
- Team management
- PDF reports

Pricing: Indie $19 | Team $79 | Enterprise $299

### GitHub Marketplace Action
Publish a scan action, charge per CI minute.
Guide: github.com/marketplace/actions/new

### VS Code Extension
Inline warnings for ^ ~ deps, missing env vars, security score in status bar.
Freemium: free basic, paid Pro ($4.99/month).

### Enterprise ($5k-50k/year)
Commercial license + SLA + private registry + security audits.

---

## PART 5: Marketing

Week 1: npm publish + GitHub + post on r/node + Hacker News Show HN
Week 2: Blog post + awesome-nodejs PR + newsletter outreach
Month 1: Product Hunt + YouTube demo + devguard.dev landing page

---

## Quick Reference

```
npm install devguard

import { runAllChecks }   from 'devguard';
import { verifyLockfile } from 'devguard/security';
import { LLMBudget }      from 'devguard/ai';
import { JWTVerifier }    from 'devguard/auth';
import { loadEnv }        from 'devguard/dx';
```
