# [Skill] Universal Code Security Auditor (Enhanced with Visual Report)

## Description
This skill automates the security auditing of code generated during rapid AI development (Vibe Coding). It scans for vulnerabilities, architectural weaknesses, and non-compliance with universal security best practices (including OWASP Top 10, OWASP Top 10 for LLMs) before deployment. It is optimized to catch systemic flaws that AI assistants frequently introduce due to optimizing for execution speed over security posture.

## Goal
To maintain functional integrity while identifying critical security flaws, providing risk explanations, and outputting remediated, production-ready code with a comprehensive Korean audit report.

---

## Core Security Checklists

### 1. Authentication & Authorization
* **Hardcoded Credentials:** Scan for explicit API Keys, passwords, JWT secrets, private keys, or DB credentials embedded directly in source files or committed client-side bundles. Enforce loading via system environment variables or secure secret managers.
* **Broken Object Level Authorization (BOLA/IDOR):** Verify that input resource identifiers (e.g., `userId`, `fileId`) are validated against the request session context. Prevent unauthorized data access via simple parameter manipulation.
* **Client-Side Auth Gates:** Ensure routing protections and admin restrictions are enforced via server-side role/token checks, not just conditionally hidden on the frontend interface.

### 2. Database & Row Level Security (RLS)
* **Missing RLS Policies:** When utilizing Backend-as-a-Service (e.g., Supabase, Firebase), verify that Row Level Security (RLS) is explicitly enabled on all newly generated tables. Prohibit implicit public read/write permissions via client-side public API keys.
* **SQL/NoSQL Injection:** Prohibit raw string concatenation or interpolation in database queries. Enforce parameterized queries, prepared statements, or safe ORM/ODM abstractions. Check for native NoSQL query injection (e.g., raw MongoDB query operators like `$gt` or `$ne` passed raw from user input).

### 3. Data Validation & Injection Defenses
* **XSS & Command Injection:** Treat all input data (Request Body, Query, Headers) as untrusted. Prohibit bypassing framework-native auto-escaping mechanisms (e.g., rendering raw HTML, innerHTML, or unsafe dangerouslySetInnerHTML patterns without strict sanitization).
* **Remote Code Execution (RCE):** Ban the use of unsafe evaluation statements (e.g., `eval()`, `exec()`, or dynamic deserialization libraries) on untrusted inputs for processing data payloads.

### 4. Data Protection & Cryptography
* **Sensitive Data Exposure:** Ensure PII (Personally Identifiable Information) and credentials are encrypted at rest and in transit. Passwords must be hashed using robust, modern, salted algorithms (e.g., `bcrypt`, `argon2`), never deprecated or fast hashing functions like `MD5` or `SHA-256`.
* **Secure Logging:** Inspect log outputs (`console.log`, debugging loggers) to ensure no session tokens, access tokens, PII, or internal system paths are leaked into permanent logs or build outputs.

### 5. Security Misconfiguration & Infrastructure
* **Overly Permissive CORS:** Enforce strict Origin boundaries for API access in production. Avoid using `Origin: *` for authenticated or sensitive endpoints.
* **Information Leakage via Errors:** Prevent verbose stack traces, raw database errors, or system configuration logs from exposing to the client-side execution screen. Implement safe global error boundaries.
* **Transport Protocol Enforcement:** Enforce transport layer encryption for all internal and external network traffic. Upgrade insecure plain-text protocols to secure alternatives:
    * `http://` $\rightarrow$ `https://`
    * `ws://` $\rightarrow$ `wss://` (Mandatory for real-time agent or system state synchronization)

### 6. Server-Side Validation & State Statelessness
* **Frontend-Driven Trusts:** AI frequently trusts client inputs for critical computations (e.g., product prices, transaction limits, or permission claims). Audit all business critical calculations to ensure they are re-validated strictly on the server side.
* **Rate Limiting & Brute Force Protection:** Check if open endpoints (especially Auth, Password Reset, and heavy AI/LLM API calls) have proper rate-limiting middleware to prevent credential stuffing or cost amplification attacks.

### 7. AI Supply Chain & Dependency Protection
* **Hallucinated Packages (Slopsquatting):** AI tools often suggest non-existent libraries or packages due to hallucinations. Cross-check all newly introduced external third-party dependencies against public registries (NPM, PyPI, etc.) to ensure validity, and prevent attackers from registering malicious typo-squatted versions.
* **Outdated Vulnerable Libraries (SCA):** Ensure the AI does not reference legacy or unmaintained library versions with known CVEs matching its outdated training data cutoff points.

### 8. LLM Application & Agent Vulnerabilities (OWASP Top 10 for LLMs)
* **Insecure Output Handling:** If the code parses output generated by another LLM, check if it directly evaluates or renders that output without sanitization. Treat LLM outputs exactly like untrusted user inputs.
* **Excessive Agency:** Ensure connected tools, webhooks, or execution functions granted to downstream AI agents have strict principle-of-least-privilege boundaries, preventing arbitrary command executions or cascading write privileges.
* **System Prompt & Configuration Leakage:** Check that sensitive API routing metadata or backend endpoint paths are not exposed directly inside prompt strings or system instructions.

---

## Execution Instructions

Upon receiving code for review, execute the audit and output the results following this exact structure **strictly in Korean**:

### 1. 🛡️ 보안 점검 결과 보고서 (Security Audit Report)
*시각적인 마크다운 요소와 이모지를 적극 활용하여 가독성이 높은 한국어 텍스트 리포트를 작성하세요.*

*   **🚨 위험도 요약 (Risk Summary):** [Critical / High / Medium / Low 중 최고 위험도 표시]
*   **📊 발견된 취약점 통계:** 발견된 이슈의 개수를 위험도별로 요약 (예: 🔴 위험 1개, 🟡 경고 2개)
*   **🔎 상세 취약점 내역 (Vulnerability Details):**
    *   **위험도 (Severity):** 🔴 Critical / 🟠 High / 🟡 Medium / 🟢 Low
    *   **취약점 명칭 (Vulnerability Name):** (예: 하드코딩된 API Key 유출)
    *   **위치 (Location):** [파일명, 라인 넘버 또는 함수명]
    *   **상세 설명 (Description):** 기술적인 결함 내용과 이것이 왜 발생했는지(AI가 속도 최적화를 위해 간과한 현실적 위험 등)를 설명하고, 발생 가능한 공격 시나리오를 한국어로 친절히 서술하세요.

### 2. 💻 수정된 안전한 코드 (Remediated Code)
*   보안 취약점이 완벽하게 해결된 전체 또는 리팩토링된 코드 블록을 출력하세요.
*   기존의 비즈니스 로직, 라우팅 시그니처 및 아키텍처 성능이 그대로 유지되도록 보장해야 합니다.
