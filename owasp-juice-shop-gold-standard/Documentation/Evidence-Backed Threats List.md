# Evidence-Backed Threats List

This document provides a **comprehensive, evidence-backed threat catalog** identified during the Security Design Review (SDR) and Threat Modeling exercise for the OWASP Juice Shop benchmark. Each entry maps directly to supporting artifacts, observed vulnerabilities, and realistic exploitation justifications.

---

## 📊 Threat Overview
- **Total Threats Identified:** 30  
- **Domains Covered:** Application & Infrastructure  
- **Risk Ratings:** Critical → Low  
- **Evidence Mapping:** All threats link to design artifacts, IaC configs, and code snippets  

---

## 🔴 Critical Risks
| Threat ID | Description | Domain | Exploitability | Evidence | Risk |
|-----------|-------------|--------|----------------|----------|------|
| **T-01** | Remote Code Execution via Insecure Deserialization | Application | Moderate | `routes/b2bOrder.ts` (yaml lib, unsanitized) | **High** |
| **T-02** | Authentication Bypass & SQL Injection | Application | Easy | `routes/login.ts` (string concat in SQL) | **High** |
| **T-03** | Unrestricted SSH (0.0.0.0/0) | Infrastructure | Moderate | `terraform/aws/main.tf` (open SSH ingress) | **High** |
| **T-04** | Overly Permissive IAM Role (s3:* on *) | Infrastructure | Trivial | `terraform/aws/iam.tf` (wildcard permissions) | **High** |

---

## 🟠 High Risks
| Threat ID | Description | Domain | Exploitability | Evidence | Risk |
|-----------|-------------|--------|----------------|----------|------|
| **T-05** | Arbitrary File Write via Upload Library | Application | Moderate | `routes/fileUpload.ts` (vuln multer) | High |
| **T-06** | Persistent XSS → Account Takeover | Application | Easy | `routes/search.ts` (reflected input) | High |
| **T-07** | JWT Signature Forgery | Application | Difficult | `lib/insecurity.ts` (weak secret, alg flaws) | High |
| **T-08** | Server-Side Request Forgery (SSRF) | Application | Moderate | `routes/profileImageUrlUpload.ts` | High |
| **T-09** | Outdated Libraries | Infrastructure | Trivial | `ftp/package.json.bak` | High |

---

## 🟡 Medium Risks
| Threat ID | Description | Domain | Exploitability | Evidence | Risk |
|-----------|-------------|--------|----------------|----------|------|
| **T-10** | Local File Read (Traversal) | Application | Moderate | `routes/fileServer.ts` | Medium |
| **T-11** | Insecure Admin Registration | Application | Easy | `routes/user.ts` (`isAdmin` unchecked) | Medium |
| **T-12** | Sensitive Disclosure via XXE | Application | Moderate | `routes/b2bOrder.ts` (XML parser misconfig) | Medium |
| **T-13** | Supply Chain Risk (Unpinned Images) | Infra | Trivial | `Dockerfile` (`node:latest`) | Medium |
| **T-14** | Container Running as Root | Infra | Moderate | `Dockerfile` (no USER directive) | Medium |
| **T-15** | Broken Access Control | Application | Easy | `routes/admin, basket.ts` | Medium |
| **T-16** | DoS via Injection Flaws | Application | Moderate | `routes/b2bOrder.ts`, `routes/search.ts` | Medium |
| **T-17** | Insecure S3 Storage | Infra | Easy | `terraform/aws/main.tf` (no encryption, public) | Medium |
| **T-18** | Business Logic Fraud | Application | Difficult | `routes/coupon.ts`, `routes/deluxe.ts` | Medium |
| **T-19** | Typosquatting Risk | Infrastructure | Trivial | `frontend/package.json` | Medium |
| **T-20** | Security Through Obscurity | Application | Moderate | `routes/blockchain.ts` | Medium |
| **T-21** | Weak Cryptography | Application | Difficult | `lib/insecurity.ts` | Medium |

---

## 🟢 Low Risks
| Threat ID | Description | Domain | Exploitability | Evidence | Risk |
|-----------|-------------|--------|----------------|----------|------|
| **T-22** | Weak Password Recovery | Application | Easy | `routes/resetPassword.ts` | Low |
| **T-23** | CAPTCHA Bypass | Application | Moderate | `routes/captcha.ts` | Low |
| **T-24** | Unvalidated Redirects | Application | Easy | `routes/redirect.ts` | Low |
| **T-25** | Hardcoded Secrets in CI/CD | Infra | Difficult | `terraform/aws/variables.tf` | Low |
| **T-26** | Open App Port (0.0.0.0/0 :3000) | Infra | Trivial | `terraform/aws/main.tf` | Low |
| **T-27** | No MFA for IAM Users | Infra | Easy | IAM policy absence | Low |
| **T-28** | Race Condition in Likes | Application | Difficult | `routes/likeProductReviews.ts` | Low |
| **T-29** | Username Tampering in Feedback | Application | Easy | `routes/feedback.ts` | Low |
| **T-30** | Verbose Error Messages | Application | Easy | `routes/errorHandler.ts` | Low |

---

## ✅ Key Takeaways
- **4 Critical Threats** (RCE, SQLi, Open SSH, IAM Wildcards) → Require *immediate remediation*.  
- **7 High Risks** → High likelihood of exploitation, often trivial.  
- **11 Medium Risks** → Important for long-term hardening and defense-in-depth.  
- **8 Low Risks** → Low severity, but provide reconnaissance vectors for attackers.  

---

## 📌 Next Steps
- Prioritize remediation of **Critical + High** threats before production.  
- Enforce **IaC security policies** (Terraform + Docker hardening).  
- Establish **secure SDLC practices** (dependency scanning, JWT hardening, input validation).  
- Adopt **threat-informed defense strategy** using this list as the baseline.
