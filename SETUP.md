# DevGuard Setup Guide

Get your project secured in under 2 minutes.

---

## Step 1: Install

```bash
npm install @devshub198211/devguard
```

Or install globally for CLI access anywhere:
```bash
npm install -g @devshub198211/devguard
```

Or run instantly without installing:
```bash
npx @devshub198211/devguard check
```

---

## Step 2: Initialize Your Project

```bash
devguard init
```

This creates:
- **`.devguardrc`** — Configuration file for security rules
- **`.devguard-memory/`** — Local storage for AI agent state
- **Security snapshot** — Baseline integrity hash of your lockfiles

---

## Step 3: Run Your First Audit

```bash
devguard check
```

You'll see a score out of 100. A perfect project scores 100/100.

---

## Step 4: Refactor Code (Free, Runs Locally)

```bash
devguard refactor src/your-file.ts
```

This opens a browser window showing:
- **Original code** on the left
- **Optimized code** on the right
- **Time complexity** analysis (e.g., O(n²) → O(n))
- **Security fixes** applied (eval removal, XSS patches)

Click "Apply" to save the changes.

---

## Step 5: Optional — Enable Cloud AI Mode

For deeper AI-powered refactoring using Google Gemini:

1. Get a free API key: https://aistudio.google.com/app/apikey
2. Set it:
   ```bash
   export DEVGUARD_AI_KEY="your_key_here"
   ```
3. Run refactor again — it will use Cloud AI automatically.

Without a key, all features still work using the built-in local analysis engine.

---

## Step 6: Add to CI/CD

Add this to your GitHub Actions workflow:

```yaml
- run: npx @devshub198211/devguard check --json > report.json
```

---

## Troubleshooting

| Problem | Solution |
|---------|----------|
| `command not found: devguard` | Use `npx @devshub198211/devguard` instead |
| `missing script` error | Don't use `npm run devguard` — use `npx devguard` |
| Refactor opens blank page | Wait 2 seconds, then refresh the browser |
| Score is 0 | Run `devguard init` first to create a baseline |

---

## Uninstall

```bash
npm uninstall @devshub198211/devguard
rm -rf .devguardrc .devguard-memory .devguard-snapshot.json
```

---

*Your custom notes below:*

<!-- Add your personal notes here -->
