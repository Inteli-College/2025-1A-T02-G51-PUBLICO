# Final Product Delivery Plan — Due **October 10, 2025**

> Goal: Deliver a functional MVP, present to HC Hospital, and submit a business plan with a user acquisition strategy.

---

## 1) Scope & Deliverables (MVP)

### D1. Form Builder (Create triage forms)

**Done means:**

* Create/edit/publish forms with: text, radio (single-choice), checkbox (multi-choice).
* Versioning per form; draft → published workflow.
* Validation (required, min/max options) & localization (pt-BR, en-US).
* Audit trail: who changed what & when.

**Acceptance tests:**

* Create a form with ≥5 questions incl. radio & checkbox; publish; form gets a shareable link/QR.
* Editing a published form creates a new version without breaking active links.
* Form JSON conforms to schema; invalid config is rejected with clear errors.

---

### D2. Form Answering (Patient & Staff)

**Done means:**

* channel **WhatsApp** (Cloud API).
* Persists answers per user (by phone) with timestamps.
* Partial progress & resume; rate limiting; basic spam protection.
* Error handling & retries for WhatsApp sends.

**Acceptance tests:**

* A patient completes a form end-to-end on WhatsApp.
* Network failure during submission recovers without duplicate entries.
* PII is stored encrypted at rest; PHI fields masked in logs.

---

### D3. Past Triages Query (Clinician Console)

**Done means:**

* Search by patient identifier (phone), date range, and form.
* Results list with key metadata; click-through to full record.
* Export selected records (CSV/JSON); access logged for audit.

**Acceptance tests:**

* Query returns the newest record first and is filterable by form & date.
* Export generates a file < 5 seconds for 1k records.
* Access appears in the audit log with user, time, and purpose.

---

### D4. LLM Summary of Triages

**Done means:**

* Summarize a single triage (SOAP-style) + optional **multi-visit longitudinal summary**.
* Hallucination guardrails: only summarize fields present; cite source fields.
* Prompt templates stored in repo; PII never leaves encrypted boundary.

**Acceptance tests:**

* Summary renders in < 2 seconds for typical forms; includes citations.
* Factuality check: random 20 triages match underlying fields ≥ 99%.

---

### D5. Deployment & HC Integration Plan

**Done means:**

* **Environments:** Dev, Staging, Prod with **IaC** (Terraform) and CI/CD.
* **Hosting target:** IBM Cloud **Code Engine** for the FastAPI container; IBM Cloud **Container Registry** for images; managed MongoDB (IBM partner or MongoDB Atlas).
* Observability: metrics, logs, uptime checks; on-call rotation doc.

**Acceptance tests:**

* Staging deploy from `main` within 10 min; rollback in 1 click.
* Health checks & logs visible; alert on error-rate > 2% over 5 min.

---

## 2) Timeline & Milestones (Aug 18 → Oct 10)

| Week (2025) | Dates           | Milestone                  | Highlights                                                          |
| ----------- | --------------- | -------------------------- | ------------------------------------------------------------------- |
| W1          | Aug 18–22       | **Kickoff & Scope freeze** | Confirm MVP backlog; architecture final; DPO & LGPD checklist draft |
| W2          | Aug 25–29       | Form Builder v1            | CRUD, validation, draft→publish, schema tests                       |                      |
| W3 - W4          | Sep 1–12        | WhatsApp Channel           | Message flow, error handling, rate limiting                         |
| W5          | Sep 15–19       | Clinician Console          | Queries, detail view, export, audit log                             |
| W6          | Sep 22–26       | LLM Summary v1             | Single-visit summary, citations, redaction toggle                   |
| W7          | Sep 29–Oct 3    | Staging hardening          | Perf pass, security pass, chaos tests, playbook                     |
| W8          | Oct 6–9         | **UAT & Dry run**          | HC user flows rehearsal; slide deck final; business plan final      |
| **D-Day**   | **Fri, Oct 10** | **HC Presentation**        | Live demo + handover package                                        |

**Cut lines:**

* **Code freeze:** Oct 7 (EOD).
* **UAT sign-off:** Oct 9 (noon).
* **Backup builds & demo data snapshot:** Oct 9 (EOD).

---

## 3) Roles & RACI (example)

| Area                | Responsible (R) | Accountable (A) | Consulted (C) | Informed (I) |
| ------------------- | --------------- | --------------- | ------------- | ------------ |
| Backend & APIs      | Alberto         | CTO             | Dev team      | Stakeholders |
| Frontend (Web)      | Dev 2           | Alberto         | UX            | Stakeholders |
| WhatsApp Flows      | Dev 3           | Alberto         | Ops           | Stakeholders |
| LLM Summaries       | Dev 4           | Alberto         | Data/ML       | Stakeholders |
| Security & LGPD     | Sec Lead        | Alberto         | Legal         | All          |
| Infra/CI/CD         | DevOps          | Alberto         | All devs      | All          |
| HC Integration      | Alberto         | CTO             | HC Tech       | HC Mgmt      |
| Presentation & Demo | Alberto         | CTO             | Team          | HC Mgmt      |
| Business Plan       | Alberto         | CTO             | Advisor       | Stakeholders |

---

## 4) Engineering & Ops Plan

**Architecture (snapshot):** FastAPI (Python) + MongoDB + WhatsApp Cloud API + LLM (provider pluggable) + Static web frontend.
**Environments:** Dev, Staging, Prod; separate DBs and secrets.
**CI/CD:** Build → Test → SAST → Container scan → Deploy (Code Engine).
**Secrets:** IBM Secrets Manager; rotated every 90 days.
**Observability:** IBM Log Analysis + alerts (latency, error rate, WhatsApp send failures).
**Backups/DR:** Daily DB backups (7d retention), weekly (4w retention); restore drill Sep 30.

**HC Integration (high level):**

* Identity: OIDC SSO readiness; fallback local users.
* Data: FHIR Patient, Observation; CSV export fallback.
* Network: IP allowlist or site-to-site VPN.
* Legal: DPA & LGPD annex; data residency in São Paulo region.

---

## 5) Quality, Security & Compliance

* **Testing:** unit ≥ 80% on core modules; integration tests for flows; load test 100 RPS for 5 min (p95 < 300ms for API).
* **Security:** OWASP top 10 review; dependency scanning; RBAC; audit logs; least-privilege DB user.
* **LGPD:** legal basis (health care); minimization; consent text; DSR workflow (access, delete); encrypted at rest & in transit; logging of data access purpose.

---

## 6) HC Hospital Final Presentation Plan

**Target length:** 25–30 min + 10 min Q\&A.
**Slides (minimum):**

1. **Project Introduction** — problem, vision, impact.
2. **What We Built** — architecture + feature overview.
3. **Demonstrated Value** — time saved, accuracy potential, pilot KPIs.
4. **Next Steps** — integration, pilot metrics, success criteria.
5. **Ask** — pilot scope, timeline, single point of contact.

**Live Demo (script):**

1. **Admin creates a triage form** (radio/checkbox/text; publish).
2. **Patient fills the form** on Web and WhatsApp; error handling showcase.
3. **Clinician queries past triages** and opens a record.
4. **One-click LLM summary** (with citations & redaction toggle).
5. **Export & integration preview** (FHIR mapping & CSV fallback).

**Demo safeguards:**

* Offline-ready video backup; feature flags; stable demo dataset; read-only prod.

---

## 7) Business Plan & New-User Prospecting

**Deliverables (Oct 10):** PDF business plan (≤15 pages), 1-page overview, pricing sheet, 5-slide investor/partner deck.

**Business plan outline:**

1. Problem & impact (Brazil triage throughput, wait times).
2. Solution & product (channels, admin, LLM summaries).
3. Market (TAM/SAM/SOM, Brazil first; expansion path).
4. Competition & differentiation (WhatsApp-first workflows, clinical guardrails).
5. Pricing (per-seat or per-location; pilot pricing for HC).
6. Go-to-market (partner with hospital networks, residency programs).
7. Regulatory & data protection (LGPD, security posture).
8. Financials & milestones (12–24 mo roadmap).
9. Risks & mitigations.

**Prospecting strategy (next 90 days):**

* **Outbound:** warm intros via HC network; LinkedIn outreach to hospital ops & IT; targeted emails with 1-page and case-study.
* **Inbound:** content posts (triage automation ROI), demo video, website with self-booked demos.
* **Partnerships:** EHR vendors & integrators; med school labs; WhatsApp Business Solution Providers.
* **Events:** healthcare IT meetups; SBIS/HL7 Brazil chapters.
* **KPIs:** #demos booked, pilot conversions, CAC, sales cycle length.

---

## 8) Risks & Mitigations

| Risk                       | Likelihood | Impact | Mitigation                                             |
| -------------------------- | ---------- | ------ | ------------------------------------------------------ |
| WhatsApp API rate limits   | M          | M      | Queueing, backoff, pre-warmed templates                |
| LLM hallucinations         | L          | H      | Field-citations, strict prompts, rule-based validation |
| Integration delays with HC | M          | H      | CSV fallback, early mapping review, weekly sync        |
| Data privacy gaps          | L          | H      | LGPD checklist, DPA, regular audits                    |
| Demo instability           | M          | M      | Freeze Oct 7, feature flags, backup video              |

---

## 9) Go/No‑Go Criteria for Oct 10

* All MVP deliverables D1–D5 passed acceptance tests in Staging.
* UAT sign-off from at least 2 HC stakeholders.
* Slide deck & business plan approved internally.
* On-call & rollback procedures documented and tested.

---

## 10) Checklists

**Pre-demo (Oct 9):**

* Refresh demo dataset & users
* Validate WhatsApp sender health
* Record backup walkthrough
* Print 1-pagers & pricing sheet

**Post-demo (Oct 10):**

* Send follow-up email & deck
* Share pilot scope & success metrics draft
* Schedule technical deep-dive with HC IT
