# DevGuard — Module Reference Guide

DevGuard is a zero-dependency toolkit containing 15 production-grade modules for Security, AI, Auth, and Developer Experience (DX).

---

## 🛡️ 1. Security Modules

### lockfile-guardian
*   **What it does:** Prevents "Supply Chain Attacks" by ensuring your `package-lock.json` hasn't been tampered with.
*   **Usefulness:** Critical for CI/CD to prevent malicious code from sneaking into your build.
*   **Example:**
    ```typescript
    import { verifyLockfile } from 'devguard';
    const result = verifyLockfile(); // Returns { valid: true/false }
    ```

### hook-scanner
*   **What it does:** Scans all `node_modules` for malicious install hooks (preinstall/postinstall).
*   **Usefulness:** Stops malware from running on your machine immediately after `npm install`.
*   **Example:**
    ```bash
    devguard scan
    ```

### token-rotator
*   **What it does:** Monitors your environment variables (NPM_TOKEN, etc.) for expiration and stale usage.
*   **Usefulness:** Reduces the risk of a leaked token being used forever.
*   **Example:**
    ```bash
    devguard check # automatically alerts if tokens are > 30 days old
    ```

### dep-pincer
*   **What it does:** Enforces "Exact Pinning" for your dependencies (removes `^` and `~`).
*   **Usefulness:** Ensures your production build is 100% identical to your local build.
*   **Example:**
    ```bash
    devguard pin --fix
    ```

---

## 🤖 2. AI & Agent Modules

### agent-schema
*   **What it does:** Type-safe JSON schema builder for LLM Tool Calling (Function calling).
*   **Usefulness:** Ensures AI models send perfectly formatted data to your backend tools.
*   **Example:**
    ```typescript
    const tool = s.obj({
      city: s.str().desc("The city name")
    });
    ```

### mcp-server-kit
*   **What it does:** Build "Model Context Protocol" servers over standard input/output.
*   **Usefulness:** Connect your custom tools and data directly to Claude, ChatGPT, or other AI agents.
*   **Example:**
    ```typescript
    const server = new MCPServerBuilder("MyServer");
    server.addTool({ name: "get_weather", handler: async () => "Sunny" });
    server.startStdio();
    ```

### refactor-engine
*   **What it does:** AI-powered security and performance refactoring with an interactive review UI.
*   **Usefulness:** One-click optimization of time complexity and security flaws.
*   **Example:**
    ```bash
    devguard refactor src/utils.ts
    ```

### agent-memory & llm-budget
*   **What they do:** Persistent history for AI agents and real-time cost/token tracking.
*   **Usefulness:** Prevents runaway AI costs and allows agents to "remember" users across sessions.

---

## 🔐 3. Auth & Identity Modules

### zero-trust-jwt
*   **What it does:** Ultra-secure JWT issuing and verification.
*   **Usefulness:** Hardened against "Algorithm Confusion" and "Replay Attacks." Uses constant-time comparisons.
*   **Example:**
    ```typescript
    const verifier = new JWTVerifier({ secret: process.env.SECRET });
    const payload = await verifier.verify(token);
    ```

### passkey-node
*   **What it does:** Zero-dependency WebAuthn (Passkey) implementation.
*   **Usefulness:** Allows you to build passwordless login that is immune to phishing.

### bot-fence
*   **What it does:** Smart rate-limiter and IP-based bot detection.
*   **Usefulness:** Protects your login endpoints from brute-force attacks.

---

## 🛠️ 4. DX (Developer Experience)

### env-safe
*   **What it does:** Startup validation for `.env` files with full TypeScript types.
*   **Usefulness:** Prevents your app from starting if a critical secret (like DB_URL) is missing or malformed.
*   **Example:**
    ```typescript
    const env = loadEnv({ PORT: s.num().default(3000) });
    ```

### log-otlp
*   **What it does:** High-performance logger that sends data to OpenTelemetry (Grafana/Honeycomb).
*   **Usefulness:** Cloud-native observability with zero external dependencies.

### api-contract
*   **What it does:** Type-safe fetch and request validation.
*   **Usefulness:** Shares types between your frontend and backend so they never "break" each other.



### Its for testing purpous if you like it leave some reviews and also if you got some sugestion you can contact me to Update it i am a student and it was an idea so let me know what i can add  more and make it more usefull for developers.
### you can contact me at [devs.hub.604@gmail.com]
