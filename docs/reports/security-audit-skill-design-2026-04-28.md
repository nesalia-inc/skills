# Security Audit Skill Design Report

**Date**: 2026-04-28
**Status**: Draft

---

## Executive Summary

The `/security-audit` skill performs comprehensive security analysis: dependency vulnerabilities, secret detection, code security patterns, and infrastructure security.

---

## Purpose

Security is critical. `/security-audit` provides:
- **Dependency scanning** — Known CVEs in packages
- **Secret detection** — API keys, tokens, passwords in code
- **Code analysis** — Common security vulnerabilities
- **Infrastructure review** — Config security
- **Compliance checking** — Security best practices

---

## When to Trigger

| Trigger | Description |
|---------|-------------|
| Manual | Human says `/security` or `/security-audit` |
| On PR | Automatic scan for new changes |
| Scheduled | Weekly security digest |
| After dependency update | `/deps-update` triggers audit |

---

## Audit Process

```
/security-audit
    ↓
┌─────────────────────────────────────────────┐
│  1. Dependency Scan                          │
│     - npm audit / pip audit / etc.           │
│     - GitHub Advisory Database              │
│     - Snyk / Dependabot alerts             │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  2. Secret Detection                        │
│     - Scan for API keys, tokens             │
│     - Passwords in code                    │
│     - Private keys                          │
│     - .env files committed                  │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  3. Code Security Analysis                  │
│     - SQL injection patterns               │
│     - XSS vulnerabilities                  │
│     - Command injection                    │
│     - Insecure deserialization             │
│     - Path traversal                       │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  4. Infrastructure Review                  │
│     - Config files (CI, Docker, etc.)      │
│     - Cloud provider configs               │
│     - Environment variable usage            │
└─────────────────┬───────────────────────────┘
                  ↓
┌─────────────────────────────────────────────┐
│  5. Generate Report                        │
│     - Severity classification              │
│     - Fix recommendations                   │
│     - Compliance status                    │
└─────────────────────────────────────────────┘
```

---

## 1. Dependency Vulnerabilities

### Scan Sources

| Source | Coverage |
|--------|----------|
| NPM Audit | Node.js packages |
| GitHub Advisories | GitHub tracked vulnerabilities |
| OSV (Open Source Vulnerabilities) | Cross-ecosystem |
| Snyk (optional) | Extended database |

### Vulnerability Report

```markdown
## Dependency Vulnerabilities

### Critical (Fix Immediately)
| Package | Version | CVE | Title | Fix |
|---------|---------|-----|-------|-----|
| json5 | 1.0.0 | CVE-2024-XXXXX | Prototype Pollution | 1.0.2 |
| express | 4.17.1 | CVE-2024-YYYYY | Request Smuggling | 4.18.3 |

### High
| Package | Version | CVE | Title | Fix |
|---------|---------|-----|-------|-----|
| semver | 7.0.0 | CVE-2024-ZZZZZ | ReDoS | 7.5.2 |

### Medium
| Package | Version | CVE | Title | Fix |
|---------|---------|-----|-------|-----|
| axios | 0.21.0 | CVE-2024-WWWWW | SSRF | 1.6.0 |

### Low
| Package | Version | CVE | Title | Fix |
|---------|---------|-----|-------|-----|
| minimatch | 3.0.4 | CVE-2024-VVVVV | Regular Expression Denial of Service | 3.0.5 |

### Summary
| Severity | Count | Patches Available |
|----------|-------|-------------------|
| Critical | 2 | 2 |
| High | 1 | 1 |
| Medium | 1 | 1 |
| Low | 1 | 1 |
```

### Fix Actions

```markdown
## Recommended Fixes

### Critical: json5
```bash
npm update json5@1.0.2
```

### Critical: express
```bash
npm update express@4.18.3
```

Run tests after update to verify no breakage.
```

---

## 2. Secret Detection

### What It Scans

```yaml
patterns:
  api_keys:
    - AWS_ACCESS_KEY
    - AWS_SECRET_KEY
    - STRIPE_KEY
    - GITHUB_TOKEN
    - SENDGRID_API_KEY

  credentials:
    - password =
    - passphrase =
    - secret =

  tokens:
    - bearer
    - jwt
    - oauth

  private_keys:
    - -----BEGIN RSA PRIVATE KEY-----
    - -----BEGIN OPENSSH PRIVATE KEY-----

  config_files:
    - .env
    - credentials.json
    - secrets.yml
```

### Secret Detection Report

```markdown
## Secret Detection

### 🚨 Secrets Found: 3

| File | Line | Type | Severity |
|------|------|------|----------|
| src/config.ts | 23 | AWS Key | CRITICAL |
| .env.example | 5 | API Key | HIGH |
| src/auth.ts | 45 | Hardcoded Password | CRITICAL |

### Details

#### src/config.ts:23
```typescript
const awsKey = "AKIAIOSFODNN7EXAMPLE" // 🚨 FOUND
```
**Recommendation**: Use environment variable instead.

#### .env.example:5
```bash
GITHUB_TOKEN=ghp_xxxxx # 🚨 FOUND (example has real format)
```
**Recommendation**: Use placeholder format: `GITHUB_TOKEN=your_token_here`

#### src/auth.ts:45
```typescript
const masterPassword = "super_secret_123" // 🚨 FOUND
```
**Recommendation**: Never hardcode passwords. Use secrets manager.
```

### Prevention

```markdown
## Prevention Measures

1. **Install pre-commit hook**
   - Detect secrets before commit
   - Uses: git-secrets, detect-secrets

2. **Add to CI**
   - Fail CI if secrets detected
   - Already configured in CI workflow

3. **Use secrets manager**
   - AWS Secrets Manager
   - GitHub Actions secrets
   - .env files (not committed)
```

---

## 3. Code Security Analysis

### Vulnerability Patterns

| Pattern | Description | Example |
|---------|-------------|--------|
| SQL Injection | Unsanitized SQL queries | `query("SELECT * FROM users WHERE id=" + id)` |
| XSS | Unescaped user input | `innerHTML = userInput` |
| Command Injection | Unsanitized shell commands | `exec("rm " + filename)` |
| Path Traversal | Unsanitized file paths | `readFile("./uploads/" + filename)` |
| Insecure Deserialization | Unsafe deserialize | `pickle.loads(data)` |
| Weak Crypto | MD5, SHA1 for passwords | `hashlib.md5(password)` |
| Hardcoded Secrets | Keys in source | `api_key = "sk_live_xxx"` |

### Code Analysis Report

```markdown
## Code Security Analysis

### SQL Injection Risk: 2 Found

#### src/db/users.ts:34
```typescript
const query = `SELECT * FROM users WHERE id = ${userId}`;
// 🚨 SQL Injection vulnerability
```
**Recommendation**: Use parameterized queries:
```typescript
const query = 'SELECT * FROM users WHERE id = $1';
await db.query(query, [userId]);
```

#### src/api/search.ts:56
```typescript
db.query(`SELECT * FROM products WHERE name LIKE '%${searchTerm}%'`);
// 🚨 SQL Injection vulnerability
```

### XSS Risk: 1 Found

#### src/components/UserProfile.tsx:28
```typescript
element.innerHTML = user.bio;
// 🚨 XSS vulnerability
```
**Recommendation**: Use textContent or sanitizers:
```typescript
element.textContent = user.bio;
// Or: element.innerHTML = sanitize(user.bio);
```

### Command Injection: 0 Found
✅ No command injection vulnerabilities detected.

### Path Traversal: 1 Found

#### src/files/download.ts:19
```typescript
const file = readFile(`./files/${filename}`);
// 🚨 Path traversal vulnerability
```
**Recommendation**: Validate and sanitize filename:
```typescript
const safeFilename = path.basename(filename);
const file = readFile(`./files/${safeFilename}`);
```

### Summary
| Category | Found | Fixed |
|----------|-------|-------|
| SQL Injection | 2 | 0 |
| XSS | 1 | 0 |
| Command Injection | 0 | - |
| Path Traversal | 1 | 0 |
| Hardcoded Secrets | 3 | 0 |
```

---

## 4. Infrastructure Security

### What It Checks

| Area | Checks |
|------|--------|
| CI/CD | Secure secrets, no hardcoded values |
| Docker | Non-root user, minimal images |
| Cloud | IAM roles, S3 bucket policies |
| Network | Firewall rules, ports exposed |
| Dependencies | Vulnerability scanning |

### Infrastructure Report

```markdown
## Infrastructure Security

### CI/CD Security: ⚠️ Issues Found

#### .github/workflows/deploy.yml:23
```yaml
env:
  AWS_SECRET: ${{ secrets.AWS_SECRET }} # ✅ Good
```
**Status**: ✅ Secrets properly configured

#### .github/workflows/deploy.yml:45
```yaml
run: |
  echo ${{ github.token }} > token.txt # ⚠️ Token in file
```
**Recommendation**: Use environment variables instead of files.

### Docker Security: ✅ Good

| Check | Status |
|-------|--------|
| Non-root user | ✅ |
| Minimal base image | ✅ |
| No secrets in image | ✅ |
| Health check | ✅ |

### Cloud Configuration: ✅ No Issues

- S3 buckets are private
- IAM roles follow least privilege
- No public resources detected
```

---

## 5. Compliance Check

### Security Checklist

```markdown
## Security Compliance Checklist

### Authentication
- [x] Strong password policy
- [x] MFA enabled
- [x] Session timeout configured
- [ ] Password rotation enforced

### Authorization
- [x] Least privilege followed
- [x] Role-based access control
- [ ] Regular access review

### Data Protection
- [x] Encryption at rest
- [x] Encryption in transit (HTTPS)
- [ ] Backup encryption
- [ ] Data retention policy

### Incident Response
- [ ] Security incident playbook
- [ ] Contact list updated
- [ ] Logging enabled
- [ ] Alerting configured

### Score: 75% (6/8 checks passing)
```

---

## Skill Usage

### `/security`

Full security audit.

### `/security --quick`

Quick scan (secrets + critical only).

### `/security --deps`

Focus on dependency vulnerabilities.

### `/security --secrets`

Focus on secret detection.

### `/security --code`

Focus on code vulnerabilities.

### `/security --report`

Detailed written report.

### `/security --fix`

Attempt to fix issues automatically.

---

## Integration with Other Skills

| Skill | Integration |
|-------|-------------|
| `/deps-update` | Triggers security audit on update |
| `/pr-review` | Reports security issues in PR |
| `/ci-fix` | Can fix some security issues |
| `/release` | Security check before release |

---

## Open Questions

1. **Tooling** — Use GitHub's built-in Dependabot? Or additional tools (Snyk, SonarQube)?

2. **Auto-fix** — What can be auto-fixed vs requires human? (Dependencies yes, code vulnerabilities no)

3. **Coverage** — Full codebase scan or only changed files?

4. **Secrets scanning** — Use git-secrets, detect-secrets, or GitHub's native scanning?

5. **Compliance** — What compliance standard? (SOC2, GDPR, HIPAA?)

---

## Related Documents

- `docs/reports/deps-update-skill-design-2026-04-28.md` — Dependency updates
- `docs/reports/ci-fix-monitor-skill-design-2026-04-28.md` — CI security

---

*This document is a design for a future skill. The skill itself has not been created yet.*
