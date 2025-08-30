# Triage System Refactor – Data Model, Flow Engine, and LLM Parsing Design

Here is a comprehensive design proposal for refactoring the triage system to create a more flexible and dynamic form logic. The proposed architecture addresses the  limitations from current aproach but is much more complex. Due to time constraints it might not be implemented but is documented here for future reference.

> Goal: a modular, versioned, and testable triage engine where (1) clinicians create reusable questions and custom enums, (2) forms are assembled as flow graphs with conditional branching, (3) patients answer in natural language that an LLM normalizes into structured types *plus* an `additional_info` field, and (4) we generate comprehensive summaries of current and past triages.

---

## 1) High‑level architecture

**Services**

* **Form Builder API** – CRUD for Questions/Enums/Forms; compile & publish immutable **FormVersions**.
* **Flow Orchestrator** – drives a **TriageSession** through a published **FormVersion** (graph), persists answers/events, computes next node via Rule Engine.
* **LLM Parsing Service** – single‑responsibility: converts raw NL answers → validated, typed **ParsedAnswer** (with confidence & `additional_info`).
* **Summary Service** – LLM + templates to generate clinical/ops summary from structured answers + past triages.
* **Webhook Adapter** – WhatsApp/Meta/Twilio glue; idempotent ingestion → Orchestrator.

**Key design choices**

* **Immutability & versioning**: sessions always pin a `form_version_id` at start; Forms are edited as drafts then **published** to FormVersions.
* **Graph model (not linear lists)**: nodes + conditional edges (DAG). Supports branches, jumps, end states.
* **Explicit types & validators**: number/text/enum (+ custom enums) with constraints; LLM must match schema.
* **Event‑sourced session log** for auditability and recovery; materialized **answers** for fast reads.

---

## 2) Data model (MongoDB‑first, Pydantic‑friendly)

### 2.1 Collections

* `enums`

  * Store user‑defined enumerations referenced by questions.
  * **Doc**: `{ _id, key: "pain_location", values: ["head","chest","abdomen",...], created_by, created_at }`

* `questions`

  * Library of reusable prompts & constraints.
  * **Doc**:

    ```json
    {
      "_id": "q_temp_c",
      "label": "Temperature (°C)",
      "type": "number",    // "number" | "text" | "enum"
      "constraints": { "min": 30, "max": 45, "precision": 1 },
      "enum_key": null,      // when type=="enum" → key in `enums`
      "nl_instructions": "If user gives Fahrenheit, convert to Celsius.",
      "metadata": { "unit": "celsius" },
      "created_by": "doctor@hc.br",
      "updated_at": "..."
    }
    ```

* `forms` (drafts)

  * Editable containers. Publishing creates a snapshot in `form_versions`.
  * **Doc**: `{ _id, title, description, owner, draft_graph, updated_at }`

* `form_versions` (immutable)

  * **Doc**:

    ```json
    {
      "_id": "fv_0012",
      "form_id": "f_covid",
      "version": 12,
      "graph": {
        "nodes": [
          {"id":"n_start","kind":"start"},
          {"id":"n_temp","kind":"question","question_id":"q_temp_c"},
          {"id":"n_cough","kind":"question","question_id":"q_cough_type"},
          {"id":"n_end","kind":"end"}
        ],
        "edges": [
          {"from":"n_start","to":"n_temp"},
          {
            "from":"n_temp","to":"n_cough",
            "when": {"all": [{"var":"answers.q_temp_c.value","op":">=","value": 37.8}]}
          },
          {"from":"n_temp","to":"n_end","when": {"else": true}},
          {"from":"n_cough","to":"n_end"}
        ]
      },
      "schema_hash": "...",  // compiled validation signature
      "published_at": "...",
      "published_by": "..."
    }
    ```

* `triage_sessions`

  * One per patient triage run, pinned to a `form_version_id`.
  * **Doc**:

    ```json
    {
      "_id": "ts_abc123",
      "user_cellphone": "55...",
      "form_version_id": "fv_0012",
      "status": "in_progress", // awaiting_form_selection | in_progress | completed | abandoned
      "current_node_id": "n_temp",
      "answers": [
        {
          "question_id": "q_temp_c",
          "value": 38.1,
          "raw_text": "101F",
          "additional_info": "Converted from Fahrenheit.",
          "confidence": 0.92,
          "captured_at": "..."
        }
      ],
      "events": [ {"type":"message_in","payload":{...},"ts":"..."}, {"type":"answer_saved",...} ],
      "created_at": "...",
      "updated_at": "..."
    }
    ```

* `summaries`

  * Store generated summaries keyed by session and also longitudinal rollups by user.
  * **Doc**: `{ _id, user_cellphone, session_id, kind: "clinical|ops|timeline", text, structured: {...}, model, created_at }`

### 2.2 Indexes

* `triage_sessions`: `{ user_cellphone: 1, status: 1, updated_at: -1 }`
* `form_versions`: `{ form_id: 1, version: -1 }` (unique on pair)
* `questions`: `{ type: 1 }`, unique on `_id` (human‑readable keys recommended)
* `summaries`: `{ user_cellphone: 1, created_at: -1 }`

---

## 3) Question/Answer schema & validation

```ts
// TypeScript-ish for clarity
export type AnswerType = "number" | "text" | "enum";

export interface QuestionDef {
  _id: string;
  label: string;
  type: AnswerType;
  constraints?: {
    min?: number; max?: number; precision?: 0|1|2|3;
    pattern?: string; // regex for text
    maxLength?: number;
    allowed_values?: string[]; // optional override for enum
  };
  enum_key?: string; // for type=="enum"
  nl_instructions?: string; // hints for the LLM parser
  metadata?: Record(string, any);
}

export interface ParsedAnswer(T=any) {
  question_id: string;
  value: T; // number | string | enum token
  additional_info?: string; // LLM interpreted text, units, qualifiers
  confidence: number; // 0..1
  raw_text: string;
}
```

Validation pipeline per answer:

1. **Schema check** by type.
2. **Constraints** (bounds/regex/allowed values).
3. **Coercions** (units, decimals → per question’s `nl_instructions`).
4. **Confidence threshold** → if `< min_conf`, ask a clarifying question.

---

## 4) Flow graph & branching DSL

### 4.1 Node kinds

* `start`, `end`
* `question` – references `question_id`.
* `compute` – run a deterministic function: e.g., `NEWS2 score`, BMI.
* `jump` – unconditional goto (useful for sections/loops avoidance).

### 4.2 Edge condition expression

* Minimal safe DSL (JSON): logical **all/any/none** over predicates.
* Predicates access prior answers by `answers.(question_id).value`.

Examples:

```json
{"all": [
  {"var":"answers.q_temp_c.value","op":">=","value":37.8},
  {"var":"answers.q_cough_type.value","op":"in","value":["productive","dry"]}
]}
```

Supported `op`: `== != > >= < <= in nin contains regex is_set is_missing`.

### 4.3 Rule evaluation

* Pure function: `(session_answers, node, edges[]) -> next_node_id`.
* Deterministic: order edges and pick first `when` that resolves `true`; support a final `{ "else": true }`.

---

## 5) Session state machine (server‑side)

States: `awaiting_form_selection → in_progress → (completed|abandoned)`.

Transitions:

1. **Start**: pick `form_version_id`, set `current_node_id` = `start`’s outbound.
2. **Message in**: Orchestrator fetches `current_node`; if `question`, call LLM Parsing; save `ParsedAnswer`; compute `next_node_id` via Rule Engine; update session.
3. **Compute nodes** run sync; results are saved as answers with `system_generated: true`.
4. **End**: mark `completed`; trigger Summary Service.
5. **Timeouts/Recovery**: if no message for N hours → `abandoned` (keep resuming from `current_node_id` on next message).

Concurrency safety: optimistic lock on `{_id, updated_at}`; retry with last event idempotency key.

---

## 6) LLM Parsing Service

**Contract**

* Input: `{ question: QuestionDef, raw_text: string, locale: "pt-BR", history: [...] }`
* Output: `ParsedAnswer` (see schema) or `need_clarification: { prompt }`.

**Prompting strategy**

* System: *“You convert patient NL to a strict JSON schema. If unsure, ask a concise clarification.”*
* Provide **JSON schema** (per question) and examples.
* Add **post‑validators**: never exceed bounds; convert units (e.g., F→C; mmHg; bpm).
* Return an `additional_info` string capturing interpretations (e.g., “self‑reported, post‑exercise”).

**Confidence**

* Derive from model logprobs or heuristic: format conformance (0.2), unit conversion (0.2), strong entity match (0.6).
* Threshold (e.g., `0.75`) drives auto‑accept vs clarify.

**Safety**

* Never hallucinate missing values; prefer `need_clarification`.

---

## 7) Summary Service (current session + longitudinal)

**Inputs**: `triage_sessions.answers`, derived compute nodes, patient metadata, last N sessions.

**Outputs**:

* `clinical`: chief complaint, onset/duration, VAS, red flags, vitals & trends, differential hints, **structured JSON** for EHR.
* `ops`: completion time, clarifications, handoff checklist, missing data.
* `timeline`: bullets of past triages with key metrics and changes.

**Technique**

* Template‑first (reduce hallucinations), small LLM pass to polish language. Cache & allow regeneration when answers change.

---

## 8) APIs & core flows (FastAPI sketch)

```py
# POST /forms -> create draft
# POST /forms/{id}/publish -> snapshot to form_versions
# POST /sessions/start {form_id}
# POST /sessions/{id}/message {text}
# GET  /sessions/{id}/summary
```

`/sessions/{id}/message` (happy path):

1. Load session & current node.
2. If node.kind=="question": call Parsing Service.
3. Save answer or return clarification prompt.
4. Evaluate edges → next node.
5. If `end` → mark completed, kick Summary Service; else return next question text.

---

## 9) Security, privacy, and LGPD hooks

* **Data minimization**: avoid storing raw transcripts; if stored, encrypt at rest.
* **PII encryption**: `user_cellphone` hashed for indexing + encrypted field copy for display.
* **Access logs & event trail**: immutable `events` array in session.
* **Retention**: TTL for abandoned sessions; configurable archive policy.
* **PHI export**: structured JSON for hospital EHR; avoid free‑text unless necessary.

---

## 10) Testing & quality gates

* **Unit**: rule evaluator, validators, compute nodes.
* **Golden tests**: given (answers) → expected path/summary.
* **Property‑based**: fuzz numeric ranges to ensure validators clamp/clarify.
* **Simulated transcripts**: NL inputs ↔ expected ParsedAnswer.

---

## 11) Migration notes (from current models)

* Keep `TriageStatus` values: `AWAITING_FORM_SELECTION`, `IN_PROGRESS`, `COMPLETED`, `ABANDONED`.
* Move `answers` from being only linear arrays to `{question_id -> latest}` map + `events` log; keep denormalized array for read APIs.
* Introduce `form_versions` and pin in `triage_sessions`.

---

## 12) Minimal Pydantic models (Python)

```py
from pydantic import BaseModel, Field, condecimal
from typing import Optional, Literal, List, Dict, Any, Union

AnswerType = Literal["number","text","enum"]

class EnumDef(BaseModel):
    key: str
    values: List[str]

class QuestionDef(BaseModel):
    id: str = Field(alias="_id")
    label: str
    type: AnswerType
    constraints: Dict[str, Any] = {}
    enum_key: Optional[str] = None
    nl_instructions: Optional[str] = None

class Node(BaseModel):
    id: str
    kind: Literal["start","end","question","compute","jump"]
    question_id: Optional[str] = None
    compute_key: Optional[str] = None

class Predicate(BaseModel):
    var: str; op: str; value: Any

class WhenExpr(BaseModel):
    all: Optional[List[Union["WhenExpr", Predicate]]] = None
    any: Optional[List[Union["WhenExpr", Predicate]]] = None
    none: Optional[List[Union["WhenExpr", Predicate]]] = None
    else_: Optional[bool] = Field(default=None, alias="else")

class Edge(BaseModel):
    from_: str = Field(alias="from")
    to: str
    when: Optional[WhenExpr] = None

class FormVersion(BaseModel):
    id: str = Field(alias="_id")
    form_id: str
    version: int
    nodes: List[Node]
    edges: List[Edge]

class ParsedAnswer(BaseModel):
    question_id: str
    value: Any
    additional_info: Optional[str] = None
    confidence: float
    raw_text: str

class TriageSession(BaseModel):
    id: str = Field(alias="_id")
    user_cellphone: str
    form_version_id: str
    status: Literal["awaiting_form_selection","in_progress","completed","abandoned"]
    current_node_id: Optional[str]
    answers: List[ParsedAnswer] = []
```

---

## 13) Tiny rule evaluator (pseudocode)

```python
def eval_pred(pred, ctx):
    v = get(ctx, pred["var"])  # e.g., answers.q_temp_c.value
    op = pred["op"]; x = pred.get("value")
    return {
        "==": v==x, "!=": v!=x, ">": v>x, ">=": v>=x, "<": v<x, "<=": v<=x,
        "in": v in x, "nin": v not in x,
        "contains": (isinstance(v, str) and isinstance(x, str) and x in v) or (isinstance(v, list) and x in v),
        "regex": re.search(x, v or "") is not None,
        "is_set": v is not None, "is_missing": v is None,
    }[op]

def eval_when(expr, ctx):
    if expr.get("else"): return True
    if "all" in expr: return all(eval_when(e, ctx) if isinstance(e, dict) and any(k in e for k in ("all","any","none","else")) else eval_pred(e, ctx) for e in expr["all"])
    if "any" in expr: return any(eval_when(e, ctx) if isinstance(e, dict) and any(k in e for k in ("all","any","none","else")) else eval_pred(e, ctx) for e in expr["any"])
    if "none" in expr: return not any(eval_when(e, ctx) if isinstance(e, dict) and any(k in e for k in ("all","any","none","else")) else eval_pred(e, ctx) for e in expr["none"])
    return True

def next_node(graph, current_node_id, ctx):
    edges = [e for e in graph["edges"] if e["from"]==current_node_id]
    for e in edges:
        if not e.get("when") or eval_when(e["when"], ctx):
            return e["to"]
    return None
```

---

## 14) Example: small branching form

```json
{
  "nodes": [
    {"id":"n_start","kind":"start"},
    {"id":"n_cc","kind":"question","question_id":"q_chief_complaint"},
    {"id":"n_pain_loc","kind":"question","question_id":"q_pain_location"},
    {"id":"n_temp","kind":"question","question_id":"q_temp_c"},
    {"id":"n_end","kind":"end"}
  ],
  "edges": [
    {"from":"n_start","to":"n_cc"},
    {"from":"n_cc","to":"n_pain_loc","when":{"any":[{"var":"answers.q_chief_complaint.value","op":"contains","value":"dor"}]}},
    {"from":"n_cc","to":"n_temp","when":{"else":true}},
    {"from":"n_pain_loc","to":"n_temp"},
    {"from":"n_temp","to":"n_end"}
  ]
}
```

---

## 15) Next steps

* Generate migration scripts to introduce `form_versions` and `triage_sessions.events`.
* Implement Orchestrator + tiny Rule Engine first (unit tests before UI).
* Stand up Parsing Service with strict JSON schema contracts for 3–5 pilot questions.
* Add Summary Service with template prompts; ship an MVP flow.
