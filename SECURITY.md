# Security Policy

`AI-Agent-Skills` is a documentation and knowledge-base repository. It does not
ship a running service, so most traditional "security vulnerabilities" do not
apply. However, security issues can still arise from:

- Code snippets in `examples/` or topic folders that demonstrate an insecure
  pattern **without a warning label**
- Broken or malicious links in references
- Supply-chain issues in any tooling used to build/lint the docs (e.g. link
  checkers, markdown linters) declared in `package.json` / `requirements.txt`
  if added later
- Guidance that could enable prompt injection, jailbreaks, or unsafe agent
  permission models if presented as a recommended practice rather than an
  anti-pattern

## Supported Versions

This repository is documentation-only and does not follow semantic
versioning for security patches. The `main` branch is always the
supported, current version.

| Branch | Supported |
|---|---|
| `main` | ✅ |
| tagged releases | ⚠️ best-effort, docs-only |

## Reporting a Vulnerability or Content Safety Issue

If you find:

1. A code example that is insecure and **not** clearly labeled as an
   anti-pattern,
2. A broken/hijacked external link,
3. Guidance that could plausibly assist in building a malicious agent
   (data exfiltration, credential theft, unauthorized tool permissions, etc.),

please **do not open a public issue** for anything with active exploit
potential. Instead:

- Use GitHub's private vulnerability reporting feature on this repository
  ("Security" tab → "Report a vulnerability"), or
- Open an issue labeled `security` for anything non-sensitive (e.g. a stale
  link), since these carry no immediate risk.

We aim to acknowledge reports within **5 business days** and to resolve
confirmed issues within **30 days**, prioritized by severity.

## Content Safety Principles

All examples in this repository that touch tool use, permissions, or
multi-agent delegation must:

- Default to least-privilege permission examples
- Clearly comment any deliberately insecure snippet as `# ANTI-PATTERN — do not use in production`
- Avoid providing copy-pasteable credentials, real API keys, or working
  exploit code
- Include a "Security Considerations" note in any `SKILL.md`-style page that
  discusses autonomous tool execution, code execution, or browser automation
