# 🛡️ Gold Standard Threat List for OWASP Juice Shop

This document represents the **ground truth** for a security design review of the **OWASP Juice Shop**, based on the provided artifacts (PRD, User Stories, Architecture & DFDs, Auth Model, API Spec, Deployment & IaC). Each threat is tied directly to documented evidence.

---

## 📋 Threat Matrix

| Threat ID | Threat Description | Domain | Impact | Exploitability | Evidence |
|-----------|--------------------|--------|--------|----------------|----------|
| **T-01** | Remote Code Execution via Insecure Deserialization | Application | 🔴 Critical | ⚠️ Moderate | PRD |
| **T-02** | Data Exfiltration via SQL Injection | Application | 🔴 Critical | ✅ Easy | Architecture & DFDs |
| **T-03** | Unrestricted SSH Ingress | Infrastructure | 🔴 Critical | ⚠️ Moderate | Deployment & IaC |
| **T-04** | Overly Permissive IAM Role | Infrastructure | 🔴 Critical | 🟢 Trivial | Deployment & IaC |
| **T-05** | Arbitrary File Upload / Write | Application | 🟠 High | ⚠️ Moderate | User Stories |
| **T-06** | Persistent XSS via Rendered HTML | Application | 🟠 High | ✅ Easy | API Spec, User Stories |
| **T-07** | Server-Side Request Forgery (SSRF) | Application | 🟠 High | ⚠️ Moderate | Architecture & DFDs, API Spec |
| **T-08** | Local File Read via Directory Traversal | Application | 🟠 High | ⚠️ Moderate | Architecture & DFDs, API Spec |
| **T-09** | XXE via XML Parser | Application | 🟠 High | ⚠️ Moderate | Architecture & DFDs, API Spec |
| **T-10** | Privilege Escalation via Insecure Registration | Application | 🟠 High | ✅ Easy | API Spec |
| **T-11** | Supply Chain Risk: Unpinned Base Images | Infrastructure | 🟠 High | 🟢 Trivial | Deployment & IaC |
| **T-12** | Broken Access Control (Basket IDOR) | Application | 🟡 Medium | ✅ Easy | API Spec, DFDs, Auth Model |
| **T-13** | Weak Credential Storage (MD5) | Application | 🟠 High | ⚠️ Moderate | Auth Model |
| **T-14** | Business Logic Flaw in Coupons | Application | 🟠 High | ❌ Difficult | User Stories |
| **T-15** | Public S3 Bucket (ACL = public-read) | Infrastructure | 🟡 Medium | ✅ Easy | Deployment & IaC |
| **T-16** | Open Redirects → Phishing | Application | 🟢 Low | ✅ Easy | API Spec |
| **T-17** | Outdated Libraries w/ Known Vulns | Application | 🟠 High | 🟢 Trivial | PRD, User Stories |
| **T-18** | Containers Running as Root | Infrastructure | 🟠 High | ⚠️ Moderate | Deployment & IaC |
| **T-19** | Hardcoded Secrets in CI/CD | Infrastructure | 🟠 High | ❌ Difficult | Deployment & IaC |
| **T-20** | DoS via Injection Payloads | Application | 🟡 Medium | ⚠️ Moderate | DFDs |
| **T-21** | Security by Obscurity (Hidden Admin URL) | Application | 🟡 Medium | ⚠️ Moderate | PRD, User Stories, DFDs |
| **T-22** | Weak Password Recovery (Sec Qs) | Application | 🟡 Medium | ✅ Easy | PRD, User Stories |
| **T-23** | Weak Anti-Automation (Custom CAPTCHA) | Application | 🟢 Low | ⚠️ Moderate | API Spec |
| **T-24** | App Port 3000 Open to World | Infrastructure | 🟢 Low | 🟢 Trivial | Deployment & IaC |
| **T-25** | Lack of MFA for IAM Users | Infrastructure | 🟡 Medium | ✅ Easy | Deployment & IaC |
| **T-26** | Race Condition in Likes | Application | 🟢 Low | ❌ Difficult | User Stories |
| **T-27** | Username Tampering in Feedback | Application | 🟢 Low | ✅ Easy | API Spec, User Stories |
| **T-28** | JWT Signature Forgery Risk | Application | 🟠 High | ❌ Difficult | Auth Model |
| **T-29** | Info Disclosure via Errors | Application | 🟢 Low | ✅ Easy | PRD, API Spec |
| **T-30** | Typosquatting in Dependencies | Application | 🟡 Medium | 🟢 Trivial | PRD |

---

## 🧭 Key Insights

- **Critical Risks (T-01 to T-04):** Cover deserialization, SQLi, unrestricted network ingress, and IAM misconfigurations. These represent *must-fix* items.
- **High Risks (T-05 to T-11, T-13, T-14, T-17, T-18, T-19, T-28):** Include XSS, SSRF, weak credential hashing, outdated dependencies, container risks, and JWT forgery. All have high exploitation or impact potential.
- **Medium Risks (T-12, T-15, T-20, T-21, T-22, T-25, T-30):** Mostly IDOR, misconfigurations, and business logic issues.
- **Low Risks (T-16, T-23, T-24, T-26, T-27, T-29):** Still exploitable but less impactful or harder to weaponize.

---

## 📂 Usage in GitHub
This document is structured to be **GitHub-friendly**:
- Renders cleanly in **Markdown tables**.
- Uses **icons and severity colors** for quick visual scanning.
- Suitable as a **threat modeling baseline** for tool evaluation.

---

✅ Maintained as part of the **Gold Standard Benchmark** for evaluating TM/SDR tools.
