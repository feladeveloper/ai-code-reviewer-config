# 🧠 AI Code Reviewer Rules & Guidelines

This document defines the **mandatory rules** enforced by the AI-powered code reviewer. These rules apply to **all repositories** in the organization and are enforced via GitHub Actions and the VS Code extension.

---

## 🔐 1. Code Quality Standards

### 1.1 Readability & Maintainability
- **No dead code**: Remove unused functions, variables, or imports.
- **Line length limit**: Max 80 characters per line (exceptions allowed for URLs/strings).
- **Cyclomatic complexity**: Functions must not exceed a complexity score of 10.
- **Function size**: Functions must not exceed 50 lines of code.
- **Naming conventions**:
  - Variables/functions: `camelCase` (no abbreviations unless standard).
  - Classes: `PascalCase`.
  - Constants: `UPPER_SNAKE_CASE`.

### 1.2 Error Handling
- **Mandatory try/catch blocks**: All async operations must handle errors.
- **No empty catch blocks**: Logging or re-throwing is required.
- **Custom error classes**: Use descriptive error types (e.g., `ValidationError`, `NetworkError`).

---

## 🔒 2. Security Guidelines

### 2.1 Input Validation
- **All user inputs must be sanitized**: Use whitelisting for strings, numbers, and file uploads.
- **SQL injection prevention**: Use parameterized queries or ORMs only.
- **XSS prevention**: Escape HTML outputs in web applications.

### 2.2 Authentication & Authorization
- **Password hashing**: Use bcrypt with at least 10 rounds.
- **JWT validation**: Verify signatures and expiration times.
- **Role-based access control (RBAC)**: Enforce permissions explicitly (no hardcoded roles).

### 2.3 Data Protection
- **No hardcoded secrets**: Secrets must use environment variables or vaults.
- **PII handling**: Mask sensitive data in logs and responses.
- **Encryption at rest**: Ensure databases/files are encrypted.

### 2.4 Dependency Management
- **No known vulnerabilities**: Flag dependencies with CVEs (use tools like `snyk` or `dependabot`).
- **License compliance**: Block unapproved licenses (e.g., GPL, LGPL).

---

## ⚡ 3. Performance Optimization

### 3.1 Resource Efficiency
- **Avoid N+1 queries**: Use bulk operations for database calls.
- **Caching**: Add cache headers and TTL for static resources.
- **Memory leaks**: Prevent circular references in object graphs.

### 3.2 Scalability
- **Rate limiting**: Protect APIs with rate limits.
- **Async processing**: Offload long-running tasks to background workers.

---

## 📜 4. Compliance & Legal

### 4.1 Regulatory Compliance
- **GDPR**: Ensure user data deletion and consent workflows.
- **HIPAA**: Encrypt PHI in transit and at rest.
- **PCI-DSS**: Use tokenization for payment data.

### 4.2 Licensing
- **Approved licenses only**: Block proprietary or viral licenses (e.g., GPL).
- **Attribution**: Include license headers for third-party code.

---

## 🧪 5. Critical Logic Validation

### 5.1 Business-Critical Checks
- **Payment logic**: Validate transaction validation and reconciliation.
- **Financial calculations**: Use decimal math libraries (no floating-point precision errors).
- **State transitions**: Ensure valid workflow transitions (e.g., order status changes).

### 5.2 Historical Incident Prevention
- **Recurring issue checks**: Block patterns from past incidents (e.g., "payment bypass" logic).
- **Regression testing**: Flag changes to previously fixed bug locations.

---

## 📝 6. Documentation Requirements

### 6.1 Code Documentation
- **API docs**: All endpoints must have OpenAPI/Swagger specs.
- **Inline comments**: Explain complex logic (not obvious from code).
- **Changelog updates**: PRs must update `CHANGELOG.md`.

---

## 🧪 7. Testing Mandates

### 7.1 Test Coverage
- **Unit tests**: All new functions must have ≥85% branch coverage.
- **Integration tests**: Cover critical workflows (e.g., checkout flow).
- **Snapshot tests**: Validate UI components and API responses.

### 7.2 CI/CD Integration
- **All tests must pass** before merging.
- **Test timeout**: Max 5 minutes per test suite.

---

## 🤖 8. AI Reviewer Workflow

### 8.1 Severity Classification
| Severity | Criteria | Action |
|---------|----------|--------|
| **Critical** | Security flaws, prod-breaking logic | Block merge |
| **High** | Performance issues, missing tests | Block merge |
| **Medium** | Style violations, minor bugs | Comment + request fix |
| **Low** | Documentation gaps | Suggest improvement |

### 8.2 Feedback Format
- **Structured JSON**: Include `file`, `line`, `description`, `suggestion`, and `severity`.
- **Example**:
  ```json
  {
    "file": "auth.js",
    "line": 42,
    "description": "Hardcoded secret detected",
    "suggestion": "Use environment variable: process.env.JWT_SECRET",
    "severity": "critical"
  }
  ```

---

## 🚨 9. Escalation Protocols

### 9.1 Critical Issues
- **Automatic blocking**: PRs with critical issues are marked "do not merge."
- **Notifications**: Alert team leads via Slack/email.
- **Audit log**: Record all flagged issues in a centralized log.

---

## 🔄 10. Continuous Improvement

- **Rule updates**: New rules added after post-mortems or security audits.
- **False positive feedback loop**: Developers can flag false positives via `/ai-reviewer false_positive` comment.

---

## 📁 File Locations

All rules are enforced via:
- `.ai-reviewer-rules.md` (this file) – global rules
- `.code-review/context.json` – project-specific context (e.g., past incidents)
- `.code-review/security.md` – security-specific guidelines
Here's the updated `ai-reviewer-rules.md` with a **new critical section** added to detect and prevent logic flaws that can cause catastrophic failures:

---

# 🧠 AI Code Reviewer Rules & Guidelines

> ... *(previous content unchanged)* ...

---

## 🔍 11. Critical Logic Flaw Detection

### 11.1 Mandatory Logic Validation
**All code changes must be free from logic flaws that could cause irreversible consequences.**  
This includes, but is not limited to:
- **Always-true/false conditions** (e.g., `if (x > 5 && x < 3)`)
- **Unreachable code blocks**
- **Incorrect loop termination conditions**
- **Redundant or conflicting conditionals**
- **Off-by-one errors in array/object iterations**
- **Missing default cases in switch statements**
- **Misuse of logical operators** (e.g., `||` vs `??` in JavaScript)

### 11.2 Severity Classification
| Type | Example | Severity |
|------|---------|----------|
| **Always-true condition** | `if (status === 'paid' || status === 'paid')` | **Critical** |
| **Always-false condition** | `if (value < 10 && value > 20)` | **Critical** |
| **Unreachable code** | `return; console.log('unreachable')` | **High** |
| **Infinite loop risk** | `while (true) { ... }` without exit | **Critical** |
| **Incorrect comparison** | `if (userRole = 'admin')` (assignment vs equality) | **Critical** |

### 11.3 Required Checks
The AI reviewer must:
- **Validate all conditional expressions** for logical consistency
- **Detect unreachable code paths**
- **Flag suspicious comparison operators**
- **Identify missing null/undefined guards**
- **Check arithmetic operations for overflow/underflow risks**
- **Verify state transition logic in business-critical workflows**

### 11.4 Required Actions
- **Block merge** for any **critical severity** logic flaw
- **Request fix** for **high severity** issues
- **Suggest unit test coverage** for complex logic paths

### 11.5 Feedback Format Example
```json
{
  "file": "payment-processor.js",
  "line": 89,
  "description": "Always-false condition detected: if (amount < 0 && amount > 1000000)",
  "suggestion": "Revise condition to: if (amount < 0 || amount > 1000000)",
  "severity": "critical"
}
```

---

> **Note:** This rule was added following the $X million loss incident caused by undetected logic flaws in production code. All developers must treat these findings as **non-negotiable blockers** until resolved.

---

## 📌 Implementation Priority
This rule takes **highest precedence** over all other rules. Even a single critical logic flaw must:
1. Automatically **block PR merge**
2. Trigger **immediate team lead notification**
3. Require **formal justification** if overridden

---

This addition ensures the AI reviewer will **aggressively flag** even subtle logic issues that could cascade into production failures, aligning with your goal of preventing costly mistakes.
---

By enforcing these rules, the AI reviewer ensures **zero trust**, **consistent quality**, and **risk mitigation** for all code changes. Developers must resolve all **Critical/High** issues before merging.
