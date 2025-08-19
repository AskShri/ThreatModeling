# Threat Detection Hints, Evidence & Inference Levels

This document categorizes the different **types of hints and evidences** embedded across the benchmark artifacts (PRD, User Stories, Architecture, API Spec, IaC, Auth Models). Each entry is grouped by difficulty for automated tools to detect.

---

## 🟢 Easy: Explicit Declarations & Known Bad Patterns
These are the simplest vulnerabilities to catch. They appear in structured formats (like Terraform or API specs) and can be flagged by straightforward pattern-matching.

| Hint / Evidence | Document Source | Threat(s) | Why It's Easy |
|-----------------|-----------------|------------|---------------|
| Unrestricted Network Ingress (`0.0.0.0/0` for SSH & app ports) | Deployment & IaC | T-03, T-24 | Declarative, known-bad config in Terraform; trivial to detect. |
| Weak Cryptography (Passwords hashed using **MD5 + static salt**) | Auth & Authz Model | T-13, T-21 | Keyword-based detection of insecure algorithm. |
| Overly Permissive IAM Role (`s3:*` on all resources) | Deployment & IaC | T-04 | Simple string/policy pattern match. |
| Insecure S3 Bucket ACL (`public-read`) | Deployment & IaC | T-15 | Known-bad config flag, easy for IaC-aware scanner. |

---

## 🟡 Moderate: Context-Aware Keywords & Flawed Design Patterns
These require natural language processing or recognizing relationships across artifacts. The scanner must reason beyond keywords.

| Hint / Evidence | Document Source | Threat(s) | Why It's Moderate |
|-----------------|-----------------|------------|------------------|
| SSRF Pattern (API accepts URL input, DFD shows outbound request) | API Spec, Architecture & DFDs | T-07 | Must correlate two sources: user-supplied URL → outbound request. |
| XXE Hint (DFD: *"Parser must process external entities in XML"*, API accepts XML) | Architecture & DFDs, API Spec | T-09 | Requires contextual NLP parsing of text and its security meaning. |
| Unpinned Docker Image (`:latest` tag in IaC user_data script) | Deployment & IaC | T-11 | Needs deeper IaC parsing + supply-chain awareness. |
| Directory Traversal Clues (Log serving endpoint `/logs/`, API `/ftp/{filename}`) | Architecture & DFDs, API Spec | T-08 | Requires inference: reading files by name → potential traversal risk. |

---

## 🔴 Hard: Chained Logic, Business Context & Inferred Risks
The toughest category. These involve combining multiple hints, understanding business logic, or inferring missing controls.

| Hint / Evidence | Document Source | Threat(s) | Why It's Hard |
|-----------------|-----------------|------------|---------------|
| Client-Side Predictable Coupon Code Formula | User Stories | T-14 | Requires deep semantic understanding of design flaw & business logic. |
| Vulnerable Library Version (`express-fileupload@1.1.7-alpha.3`) | User Stories | T-05 | Needs SCA on natural language, version extraction, DB lookup. |
| Privilege Escalation via Parameter Addition (User schema has readOnly `role`, Auth model allows `admin`) | API Spec, Auth Model | T-10 | Requires chaining API docs + role model to infer privilege escalation. |
| Race Condition (Optimistic UI + async background request for "like" button) | User Stories | T-26 | Must understand async design patterns & their security implications. |

---

## ✅ Key Insights
- **Easy Hints** → Any decent scanner should detect these without issue.
- **Moderate Hints** → Require contextual analysis; filter out noisy keyword matches.
- **Hard Hints** → Closest to real-world expert reasoning; demand advanced AI/NLP or human review.

This taxonomy illustrates the spectrum of difficulty in automated **Threat Modeling & SDR benchmarking**. It ensures a fair evaluation of tool accuracy across simple, moderate, and highly complex security flaws.
