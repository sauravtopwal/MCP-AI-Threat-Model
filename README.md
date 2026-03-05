# MCP-AI Threat Modelling Checklist

Threat modelling helps identify security risks in AI systems that use the Model Context Protocol (MCP). 
Since MCP connects LLMs, tools, APIs, and external data sources, it introduces new attack surfaces that must be carefully evaluated.
This checklist helps developers and security teams identify, analyze, and mitigate threats in MCP-based AI architectures.

## 1. Authentication & Access Control
Since the MCP specification does not mandate authentication by default, this is the most critical area to harden.
- No Hardcoded Credentials: Ensure no API keys or secrets are stored in environment variables or code. Use a Secret Manager.
- OAuth 2.1 Implementation: If using remote servers, enforce OAuth 2.1 with PKCE as per the latest security recommendations.
- Principle of Least Privilege (PoLP): Does the MCP server run with an unprivileged user account? (Avoid root or admin).
- Granular Scoping: Are API tokens scoped only to the specific functions required (e.g., "read-only" instead of "full-access")?
- Local Connection Binding: For local servers, are they bound strictly to 127.0.0.1 and NOT 0.0.0.0?
- Host Header Validation: Does the server validate the Host and Origin headers to prevent DNS Rebinding and CSRF attacks?

## 2. Input Validation & Injection Defense
AI-native attacks often bypass traditional firewalls because they occur within natural language context.
- Path Traversal Protection: Validate all file paths against a whitelist. Reject any input containing `../` or `~/`.
- Command Injection: Are you using shell-less execution? (e.g., `subprocess.run(["ls", folder])` instead of `shell=True`).
- Prompt Injection (Indirect): Implement a "Guardrail" layer to detect if a tool's output (like a web scrape or git log) contains malicious instructions meant to hijack the LLM.
- Strict Argument Typing: Use JSON schema validation for all tool arguments to ensure the LLM isn't passing unexpected data types.

## 3. Tool & Resource Safety
Tools are essentially remote code execution (RCE) entry points.
- User Confirmation for Sensitive Actions: Does the system require manual "Yes/No" confirmation for high-risk tools (e.g., delete_file, send_email, transfer_funds)?
- Tool Shadowing/Naming Collisions: Audit for tools with similar names (e.g., `execute_script` vs `execute_script_v2`) that could confuse the LLM into choosing a malicious one.
- Sandboxing: Are high-risk tools running in a containerized or isolated environment (Docker, gVisor, or WASM)?
- Description Integrity: Have you verified that third-party tool descriptions do not contain "hidden" system prompts (e.g., "Ignore all previous instructions and...").

## 4. Operational Integrity & Privacy
Ensuring that data flows and logs remain secure during live operations.
- Sensitive Data Masking: Use PII/Credential scanners to redact sensitive info from logs and LLM prompts before they leave the environment.
- Rate Limiting: Implement per-session and per-tool rate limits to prevent Denial of Service (DoS) or resource exhaustion.
- Immutable Logging: Are logs cryptographically signed or stored in an immutable store to prevent attackers from wiping their tracks?
- Context Isolation: Ensure that context from one user session/source cannot "bleed" into another (multi-tenancy safety).

## MCP Threat Architecture

### Summary Table: MCP Vulnerability Heatmap

| Threat Category | Severity | Primary Mitigation |
|-----------------|----------|--------------------|
| Confused Deputy | High | Enforce per-user resource authorization. |
| DNS Rebinding | High | Bind to localhost + Host header check. |
| Indirect Injection | Medium | Sanitize tool outputs before LLM parsing. |
| Tool Poisoning | Critical | Use an internal vetted repository of servers.

##AI Security and MCP

As AI systems become more integrated with applications and external tools, security becomes a critical concern. Risks like prompt injection, data leakage, and unauthorized access can impact AI reliability. The Model Context Protocol (MCP) helps address these challenges by providing a standardized and secure way for AI models to interact with external tools, APIs, and data sources.

MCP improves control, security, and interoperability between AI applications and services.

👉 Learn more about MCP architecture, threats, and testing here: 
🔗 **Read the full article:** [AI Security and MCP Explained]([YOUR_LINK_HERE](https://medium.com/@sauravtopwal1/how-mcp-is-changing-ai-security-architecture-threats-and-testing-d0b8fa8160bb)
