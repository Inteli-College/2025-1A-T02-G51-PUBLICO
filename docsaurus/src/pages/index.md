---
title: Home
---
# Introduction

Hospitals everywhere face the same bottleneck at the front door: demand rises faster than staff capacity. The result is crowded emergency areas, long waits, uneven patient experience, and constant pressure on clinicians. Triage is designed to sort urgency quickly and fairly, but today it is often manual, variable across teams, and burdened by fragmented data entry.

**Triage AI** addresses this gap with an AI-assisted triage intake and summarization workflow that runs where patients already are (e.g., WhatsApp). The system collects symptoms and context in natural language, structures the information consistently, and produces a clear summary for clinicians—without replacing clinical judgment. It is designed to be safe, auditable, and easy to integrate with existing hospital processes.

---

## The current state of affairs (pain points)

* **Overloaded intake**: Nurses and physicians spend precious time on repetitive data gathering, documentation, and re-typing the same history across systems.
* **Inconsistency**: Triage quality varies by shift, experience, and workload; unstructured notes make downstream care slower.
* **Fragmented tools**: Messaging apps, phone calls, and paper forms don’t flow into digital records in a structured way.
* **Patient frustration**: Long waits with little feedback and no clear next step undermine trust and outcomes.

---

## Our solution (what we’re building)

**An AI-assisted triage intake pipeline** that:

1. **Collects** patient-reported information conversationally (e.g., WhatsApp).
2. **Structures** it into standardized, clinically relevant fields.
3. **Summarizes** the case for rapid review by clinicians.
4. **Routes** the result to hospital systems or queues to support prioritization and next actions.

Behind the scenes, the solution uses a message broker and worker pattern so the webhook that receives the patient message stays fast and reliable, while an AI service performs the heavier language processing in the background. Outputs are stored securely (e.g., PDFs of summaries, structured triage data) for auditability and follow-up.

> **Clinician-in-the-loop:** AI suggestions never replace medical judgment; they accelerate it. Every triage recommendation is transparent, traceable, and reviewable.

---

## Who benefits (three personas)

### 1) Hospitals (Operations & Management)

* **Throughput & flow:** Faster, standardized intake shortens bottlenecks and improves time-to-first-decision.
* **Consistency at scale:** Uniform data structures enable fairer prioritization and easier reporting.
* **Resource allocation:** Clear urgency signals help align staff, rooms, and diagnostics with demand.
* **Audit & compliance:** Structured records and artifacts support governance and quality programs.

### 2) Doctors & Nurses (Clinical Teams)

* **Less clerical burden:** AI-generated summaries and pre-filled fields reduce typing and repetition.
* **Faster context-grasp:** Consolidated, noise-reduced histories surface red flags early.
* **Decision support:** Consistent triage criteria and checklists lower variability under pressure.
* **Traceability:** Every step leaves a reviewable trail; clinicians stay in control.

### 3) Patients

* **Quicker first touchpoint:** Intake starts immediately from a familiar channel; fewer line waits for basic history.
* **Clarity of next steps:** Triage feedback (e.g., “urgent—please come now” vs. “schedule visit”) improves trust.
* **Accessibility:** Conversational intake reduces barriers for those uncomfortable with forms or portals.

---

## Expected gains and how we’ll measure them

* **Reduced time to first triage decision**
  *KPI:* median minutes from first contact to triage category assignment.
* **Higher intake productivity**
  *KPI:* number of patient intakes per hour per triage nurse/physician.
* **Improved documentation quality**
  *KPI:* completeness score of key fields; reduction in missing/ambiguous data.
* **More consistent prioritization**
  *KPI:* variance of triage categories across shifts for comparable presentations.
* **Patient experience**
  *KPI:* time-to-first-response and satisfaction feedback after intake.

---

## Guardrails and principles

* **Privacy & security by design:** Data minimization, encryption in transit/at rest, strict access controls, and compliance with applicable regulations (e.g., LGPD).
* **Safety:** Conservative prompts, clinician oversight, and clear disclaimers—no autonomous diagnoses.
* **Interoperability:** Structured outputs designed to integrate with existing systems and workflows.
* **Observability:** Metrics and logs to trace each case from message to summary, enabling continuous improvement.

---

## In one sentence

**Triage AI** turns messy, time-consuming first contact into consistent, clinician-ready summaries—so hospitals flow better, doctors focus on care, and patients get faster, clearer guidance.