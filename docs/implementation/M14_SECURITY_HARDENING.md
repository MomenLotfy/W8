# W8 — M14 Security Hardening

Status: Implementation Specification
Milestone: M14

---

## 1. Objective

Harden W8 against realistic application, infrastructure, authentication, API, content and AI threats.

Security is not a final checklist.

Security controls must exist at:

- Flutter
- API
- Database
- Admin
- Infrastructure
- CI/CD
- AI layer
- Third-party integrations

---

## 2. Security Principles

1. Zero trust.
2. Least privilege.
3. Defense in depth.
4. Secure by default.
5. Fail closed.
6. Validate at boundaries.
7. Never trust client input.
8. Minimize sensitive data.
9. Secrets never enter source control.
10. Security events are auditable.

---

## 3. Threat Model

Threat categories:

### Authentication attacks
- credential stuffing
- brute force
- session theft
- token theft
- account enumeration

### Authorization attacks
- IDOR
- privilege escalation
- role bypass
- tenant/user boundary violations

### API attacks
- injection
- malformed payloads
- excessive requests
- resource exhaustion

### Admin attacks
- compromised admin account
- privilege abuse
- unauthorized content changes

### Content attacks
- malicious media
- unsafe content
- malformed files

### AI attacks
- prompt injection
- jailbreak attempts
- data exfiltration
- unsafe recommendations
- tool abuse

### Infrastructure
- exposed secrets
- vulnerable dependencies
- insecure containers
- exposed ports

---

## 4. Input Validation

Every external input must be validated.

Sources:

- JSON body
- query parameters
- path parameters
- headers
- uploaded files
- webhook payloads

Validation occurs server-side even if Flutter validates first.

---

## 5. Injection Protection

Use parameterized queries/ORM.

Never construct SQL using string concatenation.

For search:

- sanitize
- parameterize
- limit result size

---

## 6. API Rate Limiting

Rate-limit sensitive endpoints:

- login
- password reset
- OTP
- AI requests
- search
- notification endpoints
- admin APIs
- webhooks where appropriate

Rate limits must be configurable.

---

## 7. Authentication Security

Requirements:

- short-lived access credentials
- secure refresh mechanism
- token rotation where applicable
- revocation support
- secure storage on device
- brute-force protection

---

## 8. Authorization

Authorization must be checked server-side.

Every protected resource must verify:

```text
Authenticated?
↓
Correct user?
↓
Correct role?
↓
Correct permission?
↓
Allowed action?
```

Never rely on hidden UI elements as authorization.

---

## 9. IDOR Prevention

Example:

```text
User A requests:
GET /users/B/profile
```

Backend must verify ownership/permission.

UUIDs are not authorization.

---

## 10. Admin Security

Admin requires:

- dedicated authentication
- RBAC
- permission checks
- audit logs
- stronger session controls
- rate limits

Sensitive operations require confirmation.

High-risk operations may require re-authentication.

---

## 11. Secrets Management

Secrets include:

- JWT secrets
- database credentials
- Redis credentials
- AI provider keys
- notification keys
- payment webhook secrets
- OAuth secrets

Never:

- commit secrets
- place secrets in Flutter
- hardcode them
- expose them in logs

---

## 12. Dependency Security

CI must run:

- dependency vulnerability scanning
- secret scanning
- static analysis
- container scanning

Recommended:

- Trivy
- Gitleaks
- language-specific audit tools
- SAST

---

## 13. Container Security

Containers must:

- use minimal base images
- avoid unnecessary packages
- run as non-root
- pin dependencies where appropriate
- expose only required ports
- avoid embedded secrets

---

## 14. Database Security

Requirements:

- private network access
- least-privilege DB user
- encrypted connections where applicable
- migrations controlled through CI/CD
- backups
- restore testing

Application user should not have unrestricted database administration privileges.

---

## 15. File Upload Security

If W8 accepts media:

Validate:

- MIME type
- file size
- extension
- content signature

Do not trust client-provided MIME type.

Store uploaded files outside executable paths.

Generate safe filenames.

---

## 16. AI Security

AI input is untrusted.

Never allow user prompts to override system safety policies.

Separate:

- System instructions
- Developer rules
- User input
- Retrieved content
- Tool outputs

Tool permissions must be explicit.

AI must not directly execute privileged administrative operations.

---

## 17. AI Output Validation

AI-generated:

- workout recommendations
- nutrition recommendations
- health-related statements

must pass through application-level validation where required.

The LLM cannot be the final authorization or safety layer.

---

## 18. Health & Fitness Safety

W8 must avoid presenting AI recommendations as medical diagnosis.

High-risk scenarios should trigger safe handling.

Examples:

- severe pain
- injury
- concerning symptoms
- eating disorder-related requests
- unsafe weight-loss requests
- extreme training

AI should recommend appropriate professional help when required.

---

## 19. Mobile Security

Flutter:

- secure credential storage
- no secrets in app bundle
- no production API keys embedded unnecessarily
- certificate/network security as appropriate
- disable verbose production logs
- avoid sensitive data in local storage

---

## 20. Transport Security

Production communication must use HTTPS.

Reject insecure API endpoints in production configuration.

---

## 21. Security Headers

API infrastructure should configure appropriate headers including:

- HSTS
- content type protection
- frame protection where relevant
- restrictive CORS

CORS must use allowlists, not wildcard in production where credentials are involved.

---

## 22. CORS

Development may use controlled localhost origins.

Production must specify exact allowed origins.

Never:

```text
Access-Control-Allow-Origin: *
```

with credentialed requests.

---

## 23. Security Testing

Required:

### Automated
- SAST
- dependency scan
- secret scan
- container scan

### Application
- authentication tests
- authorization tests
- IDOR tests
- rate limit tests
- input validation tests

### Manual
- threat-model review
- privilege escalation testing
- admin abuse testing

---

## 24. Security Incident

Security incident flow:

```text
Detect
↓
Contain
↓
Preserve evidence
↓
Revoke credentials if needed
↓
Patch
↓
Recover
↓
Investigate
↓
Postmortem
```

---

## 25. Definition of Done

M14 is complete when:

- Threat model documented
- Authentication hardened
- Authorization verified
- IDOR protections tested
- Rate limiting exists
- Secrets externalized
- Dependency scanning exists
- Secret scanning exists
- Container scanning exists
- Admin hardened
- AI security boundaries exist
- Upload security exists
- Security tests pass
- Production security configuration documented
