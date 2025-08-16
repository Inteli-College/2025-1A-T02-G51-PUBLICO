---
title: Module Planning
description: Planning for Module 3 Sprint 1
---
# Recovery Plan — Delayed Tasks (Semester 2025.2)

> Goal: document the scope, impact, blockers, and next steps for the critical tasks of the WhatsApp/LLM triage project, organizing execution and acceptance criteria.

---

## Table of Contents

- [Recovery Plan — Delayed Tasks (Semester 2025.2)](#recovery-plan--delayed-tasks-semester-20252)
  - [Table of Contents](#table-of-contents)
  - [Overview](#overview)
  - [1) Logic to save answers](#1-logic-to-save-answers)
  - [2) Logic to **send the next message** after an answer](#2-logic-to-send-the-next-message-after-an-answer)
  - [3) **Doctor creates a form via WhatsApp**](#3-doctor-creates-a-form-via-whatsapp)
  - [4) **LLM summary flow**](#4-llm-summary-flow)
  - [Cross-cutting dependencies and techniques](#cross-cutting-dependencies-and-techniques)
  - [Suggested schedule (biweekly sprints)](#suggested-schedule-biweekly-sprints)
  - [Risks and mitigations](#risks-and-mitigations)
  - [Definition of Done (DoD) — common to all tasks](#definition-of-done-dod--common-to-all-tasks)
  - [Appendix — Suggested structures (summary)](#appendix--suggested-structures-summary)

---

## Overview

| Task                                 | Priority | Impact |   Risk | Dependencies                                   | Effort | Status  |
| ------------------------------------ | -------- | -----: | -----: | ---------------------------------------------- | -------- | ------- |
| 1) Logic to save answers             | High     |   High | Medium | MongoDB, `FormAnswers` model, WhatsApp Webhook | M        | Delayed |
| 2) Send next message after an answer | High     |   High | Medium | Task 1, stable Webhook, queue/lock             | M        | Delayed |
| 3) Doctor creates form via WhatsApp  | Medium   |   High | Medium | Auth, parsing, persistence                     | M–L      | Delayed |
| 4) LLM summary flow                  | Medium   | Medium |   High | Consolidated data, prompts, cost               | M        | Delayed |

Effort: P (small), M (medium), G (large).

---

## 1) Logic to save answers

**Description**
Persist patient answers by `formId` and `userCellphone`, update `FormQuestionPosition`, ensure idempotency (avoid duplicates on retries), and maintain `created_at/updated_at`.

**Acceptance criteria**

* [ ] On valid input, create/update a single document per (`formId`, `userCellphone`).
* [ ] `FormQuestionPosition` increments by exactly +1 per valid answer.
* [ ] Idempotency: if the same payload arrives again (WhatsApp retry), the system does not duplicate the answer.
* [ ] Type validation: `text` is free-form; `radio` must match one of the `options`.
* [ ] DB indexes: composite index on (`formId`, `userCellphone`) and optional TTL for inactive drafts.
* [ ] `updated_at` always reflects the latest modification.

**Common blockers**

* Concurrent write conflicts (same user responding in multiple instances).
* Nested question arrays / heterogeneous structures.
* `ObjectId` serialization errors (pydantic / dumps).

**Next steps**

* [ ] Create unique index (`formId`, `userCellphone`).
* [ ] Implement idempotent upsert (dedupe key based on `message_id`).
* [ ] Normalize question structure (flat array; explicit `position`).
* [ ] Load tests with retries and out-of-order events.

**Metrics**

* Persistence error rate < 0.5%.
* Duplicates per 1,000 messages < 2.

---

## 2) Logic to **send the next message** after an answer

**Description**
After processing an answer, determine and send the next question based on `FormQuestionPosition`, including simple branching (e.g., if pain ≥7, raise priority).

**Acceptance criteria**

* [ ] After persisting the answer (Task 1), the system sends the next question within **2 s** under normal conditions.
* [ ] “End of form” supported with a wrap-up message + instructions.
* [ ] Debounce/dedup: only one send if the same event arrives twice.
* [ ] Configurable inactivity timeout (e.g., 60 min → reminder).
* [ ] Invalid input handling: corrective message + resend of the same question.

**Blockers**

* WhatsApp API rate limits.
* Concurrency (short locks per user+form).
* Missing mapping from position → question (when there are groups/nesting).

**Next steps**

* [ ] Introduce a **queue/job** layer (e.g., Redis Queue or Celery) for async sends.
* [ ] Implement short-lived lock per (`formId`, `userCellphone`).
* [ ] Deterministic `next_question(position)` mapping.
* [ ] Structured logs (`request_id`, `message_id`, `user`).

**Metrics**

* P95 latency “answer → next question” < 1.5 s.
* Unintended duplicate sends per user < 1/week.

---

## 3) **Doctor creates a form via WhatsApp**

**Description**
Conversational flow so the doctor can create/edit/publish forms without a dedicated web UI.

**Proposed flow (macro)**

1. **/new_form** → ask for title.
2. **/add_question** → collect: `question_text`, `type` (`text`/`radio`), `options` (if `radio`).
3. **/list** → show numbered structure.
4. **/remove {n}** and **/move {n} {new_n}**.
5. **/publish** → generate `formId` and link/ref.
6. **/preview** → send a simulated flow to the doctor.

**Acceptance criteria**

* [ ] Type validation: only `text` and `radio` (for now).
* [ ] Transactional persistence: form is **published** only after an explicit command.
* [ ] **/cancel** discards the draft.
* [ ] Security: bind the doctor’s number to WABA (allowlist / token).
* [ ] `updated_at` changes on every modification.

**Blockers**

* Reliable authentication of the doctor’s phone number.
* Conversational UX limits (message length, many options).

**Next steps**

* [ ] Implement a state machine for the “Form Builder”.
* [ ] Integrity checks (min 1 question; `radio` with ≥2 options).
* [ ] **/duplicate {formId}** to reuse forms.

**Metrics**

* Avg time to create/publish a form < 3 min.
* Validation errors per creation < 3%.

---

## 4) **LLM summary flow**

**Description**
Generate a **structured clinical summary** at the end of the form: chief complaint, pain (0–10), red flags, initial recommendation, and **risk flags**.

**Acceptance criteria**

* [ ] Prompt with a **fixed structure** (JSON) including: `chief_complaint`, `pain_score`, `red_flags`, `priority`, `next_steps`.
* [ ] **No hallucinations**: the LLM only uses collected data; missing fields must be `null`.
* [ ] Token/cost budget and fallback (smaller model) on provider issues.
* [ ] Prompt and response logs **anonymized** (LGPD).
* [ ] Summary available in the patient’s history (linked to `formId`).

**Blockers**

* Inconsistent data (incomplete form).
* Costs and provider quotas.

**Next steps**

* [ ] Create prompt template and tests with **synthetic cases** (e.g., high pain, fever, dyspnea).
* [ ] Validate JSON output and normalize values (e.g., “38°C” → 38.0).
* [ ] Guardrails: list of disallowed claims (definitive diagnosis).

**Metrics**

* Valid JSON parse rate ≥ 99%.
* Generation time P95 < 1.5 s (local/cached model) or < 5 s (external API).

---

## Cross-cutting dependencies and techniques

* **Database**: MongoDB with indexes:
  - `unique: formId+userCellphone`
  - `secondary: updated_at`
* **Idempotency**: key by `message_id` (WhatsApp) + content hash.
* **Queue/Jobs**: decouple webhook from sending (resilience and throughput).
* **Observability**: structured logs (JSON), metrics (latency, error rate), traces by `correlation_id`.
* **Security/LGPD**: encryption at rest, PII masking in logs, minimal necessary retention.
* **Tests**: unit (models/validations), integration (webhook → DB → next send), e2e simulating real journeys.

---

## Suggested schedule (biweekly sprints)

* **Sprint 1 (Aug 04–Aug 15)**: Task 1 complete + indexes + idempotency tests.
* **Sprint 2 (Aug 18–Aug 29)**: Task 2 with queue/lock + latency SLAs.
* **Sprint 3 (Sep 1–Sep 12)**: Task 3 (conversational builder) + doctor auth.
* **Sprint 4 (Sep 15–Sep 26)**: Task 4 (LLM) + guardrails + JSON schema.
* **Sprint 5 (Sep 29–Oct 10)**: Hardening, observability, e2e, performance, **IBM Cloud production-like deploy** & **MVP demo to HC**.

> **Release Gate**: only proceed if the previous sprint meets 100% of the defined acceptance criteria and metrics.

---

## Risks and mitigations

* **Concurrency and duplicates** → short locks, idempotency via `message_id`, exponential retries.
* **WhatsApp API changes** → wrap calls in our own client and version it.
* **LLM cost and latency** → summary cache, smaller fallback model, “cascade” orchestration.
* **Form quality** → curated templates + builder validations.

---

## Definition of Done (DoD) — common to all tasks

* [ ] Acceptance criteria covered by automated tests.
* [ ] Observability with useful metrics and logs.
* [ ] Documentation (README + flows) updated.
* [ ] Feature flag / simple rollback.
* [ ] Security and LGPD checked (minimal PII, anonymized logs).

---

## Appendix — Suggested structures (summary)

**`form_answers` document (unique key: `formId+userCellphone`)**

```json
{
  "formId": "ObjectId",
  "userCellphone": "string",
  "answers": [
    { "position": 1, "question_id": "ObjectId", "type": "radio", "answer": "string" }
  ],
  "FormQuestionPosition": 3,
  "created_at": "ISO",
  "updated_at": "ISO",
  "last_message_id": "string"
}
```

**LLM output (JSON schema)**

```json
{
  "chief_complaint": "string|null",
  "pain_score": "number|null",
  "red_flags": ["string"],
  "priority": "low|medium|high|critical",
  "next_steps": ["string"]
}
```

---
