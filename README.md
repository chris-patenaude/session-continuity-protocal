# Session Continuity Protocol (SCP)

Session Continuity Protocol (SCP) is a lightweight, repeatable method for preventing “session amnesia” when using large language models across multiple chat sessions on the same project. It treats each new session as equivalent to onboarding a capable new developer who has not attended prior meetings: without a structured handoff, prior decisions, constraints, and progress are lost, leading to incorrect assumptions, drift, rework, and token inefficiency. SCP addresses this by establishing a small set of durable, versioned artifacts—primarily a Project Memory Pack (PMP) and an ADR-lite Decision Log—plus an optional changelog, and by enforcing a “no silent changes” rule so that key decisions and invariants cannot change without explicit documentation and rationale. The result is a consistent, auditable project state that can be loaded at the start of every session and updated at the end, enabling faster restarts, better alignment, and reliable multi-session execution.

**Audience:** Project developers and technical leads using LLMs across multiple sessions for design, implementation, analysis, documentation, and planning.

**Purpose:** Provide a repeatable process and set of artifacts that preserve project state across sessions, reducing drift, rework, and token waste.

**Core principle:** Chat history is not state. State must be explicit, versioned, and reloadable.

---

## 1) Problem Statement

Large or complex tasks frequently exceed a single session’s context window. When work is split across multiple sessions, the next session often starts “cold,” resulting in:

- **Loss of decisions and state:** The new session lacks prior choices, rationale, progress, and constraints.
- **Incorrect assumptions:** The model fills gaps with plausible but wrong defaults.
- **High token cost to reload context:** Pasting full history is expensive and still misses tacit decisions.
- **Drift and rework:** Output becomes inconsistent with earlier work, requiring corrections and repeated debate.

This phenomenon is referred to as **session amnesia**.

---

## 2) Analogy: Each New Chat Session Is a New Hire’s First Day

A practical way to visualize session amnesia is to treat each new session as onboarding a new employee who is capable but has not attended earlier meetings.

- Without onboarding materials, a new hire will **re-decide** old decisions, misread constraints, and produce output that is reasonable in isolation but **misaligned** with the project’s established direction.
- Effective teams solve this with an onboarding packet, meeting notes, and change control. SCP applies the same discipline to AI sessions.

Mapping:

- **Project Memory Pack (PMP)** = onboarding brief + current status
- **Decision Log (ADR-lite)** = key decision history and rationale
- **No Silent Changes gate** = change control
- **Closeout (version + changelog)** = end-of-day handoff so the next “new hire” starts aligned

---

## 3) Solution Overview

Implement **Session Continuity Protocol (SCP)**: a workflow that makes continuity a first-class deliverable.

SCP uses two required artifacts and one recommended artifact:

1. **Project Memory Pack (PMP)** (required)
   Compact, structured, versioned project state.

2. **Decision Log (ADR-lite)** (required)
   Short decision records capturing rationale to prevent re-litigating choices.

3. **Changelog/Diff** (recommended)
   Small delta summary of changes since the prior version.

SCP also enforces a key control:

- **No Silent Changes:** Invariants and key decisions cannot change without explicit documentation.

---

## 4) Definitions

### 4.1 Project Memory Pack (PMP)

A concise document that represents the project’s true state: objective, scope, constraints, invariants, key decisions, current status, interfaces, risks, open questions, and next actions.

### 4.2 Invariants

Statements that must not change unless explicitly approved (e.g., “Database is PostgreSQL,” “Auth token TTL is 15 minutes,” “API paths are stable”).

### 4.3 Decision Log (ADR-lite)

A record of any non-trivial decision including rationale and consequences. This preserves tacit intent that otherwise disappears between sessions.

---

## 5) Required Artifacts

### 5.1 Project Memory Pack (PMP) Template (Required)

```text
PROJECT MEMORY PACK v{N} (Last updated: {YYYY-MM-DD})

1) Objective
- ...

2) Scope
- In scope: ...
- Out of scope: ...

3) Constraints
- Hard constraints: ...
- Preferences: ...

4) Invariants (must not change without explicit decision)
- ...

5) Key Decisions (with rationale)
- D1: {decision} — because {rationale}
- D2: ...

6) Current State
- Completed: ...
- In progress: ...
- Blocked: ...

7) Interfaces / Artifacts (source of truth)
- API endpoints / function signatures:
  - ...
- Data schemas:
  - ...

8) Known Issues / Risks
- ...

9) Open Questions (owner + target date if applicable)
- Q1: ...
- Q2: ...

10) Next Actions (ordered, each with acceptance criteria)
- A1: ... (Done when: ...)
- A2: ...
```

**Requirements**

- Keep it concise and stateful (target: 300–900 tokens).
- Edit sections instead of appending narrative.
- Treat it as the **single source of truth**.

---

### 5.2 Decision Log (ADR-lite) Template (Required)

```text
DECISION D{N} (YYYY-MM-DD)
Context: ...
Decision: ...
Alternatives considered: ...
Reasoning: ...
Consequences / tradeoffs: ...
Reversal conditions: ...
```

**When to create a decision entry**

- Tech stack choices (DB, framework, hosting).
- Architecture decisions (monolith vs services).
- Interface/schema/contract changes.
- Performance/security decisions.
- Any change to an invariant or reversal of a prior key decision.

---

### 5.3 Changelog/Diff Template (Recommended)

```text
CHANGELOG (since v{N-1})
- Updated Current State: moved A4 to Completed
- Added Decision D7: chose PostgreSQL over MongoDB
- Clarified Invariant #3: auth token TTL fixed at 15m
```

Purpose: allow new sessions to quickly understand what changed without re-reading everything.

---

## 6) Operating Procedure

### 6.1 Startup Procedure (Bootstrap)

At the start of every session, load the latest PMP (and relevant decisions if not embedded).

**The model must output**

1. Restated constraints and invariants (explicitly).
2. A plan tied to existing decisions (reference decision IDs where possible).
3. The first concrete step.

#### Bootstrap Prompt (Paste into every new session)

```text
You are continuing an ongoing project. Treat the Project Memory Pack below as the source of truth.

Rules:
1) Do not change any Invariants or Key Decisions unless you explicitly propose a new Decision record explaining why.
2) If something is missing, state it as an assumption and proceed with the safest default consistent with the Memory Pack.
3) Start by producing: (a) constraints+invariants restatement, (b) plan tied to decisions, (c) first concrete step.

<Project Memory Pack>
...paste latest...
</Project Memory Pack>
```

---

### 6.2 Change Control: “No Silent Changes” Gate

The model is not allowed to “quietly optimize” by changing fundamentals.

If it wants to change any item in **Invariants** or **Key Decisions**, it must:

1. Name the invariant/decision being changed,
2. Provide a rationale,
3. Add a new ADR-lite decision record documenting the change.

---

### 6.3 Closeout Procedure (End of Session)

At session end, produce and persist:

1. Updated PMP with version increment (vN → vN+1),
2. Any new Decision entries,
3. Changelog vs prior PMP version.

#### Closeout Prompt

```text
Before we end, produce:
1) Updated Project Memory Pack (increment version)
2) Any new Decision entries (ADR-lite)
3) A short changelog vs prior version
```

---

## 7) Quality Controls (Recommended)

### 7.1 Consistency Check Block

Require this at the start of the model’s response:

```text
CONSISTENCY CHECK
- I will not change: {list invariants}
- My plan references decisions: {D1, D4, ...}
- Assumptions I am making: {A1, A2}
- If an assumption is wrong, my fallback is: {fallback action}
```

This surfaces hidden assumptions before they cause rework.

### 7.2 Acceptance Criteria for Next Actions

Each “Next Action” must include a “Done when” clause.

Examples:

- “Done when: endpoint returns 200 with schema X and contract tests pass.”
- “Done when: latency p95 < 200ms on dataset Y.”

---

## 8) Implementation Options

### 8.1 Low-Tech (Copy/Paste or Wiki)

**Best for:** Small teams, early-stage work.

- Store PMP in a shared document (README, Confluence, Notion).
- Paste PMP into each new session.
- Update PMP at closeout and paste the new version back into storage.

**Common failure mode:** PMP grows too large. Enforce brevity and move historical details to ADR/changelog.

---

### 8.2 Repo-Based (Recommended)

**Best for:** Most engineering teams.

Suggested structure:

- `docs/project_memory_pack.md`
- `docs/decisions/` (or `docs/adr/`)
- `docs/changelog.md` (optional)

Process:

- Update PMP/ADR as part of the development workflow.
- Review changes like code (diffs are meaningful).
- Add a PR checklist item: “If multi-session AI was used, update PMP/ADR.”

---

### 8.3 System/Agent-Based (Production)

**Best for:** High-volume AI use or integrated agent tooling.

Persist PMP as structured canonical data, render for humans if needed.

Example canonical JSON state:

```json
{
  "version": 12,
  "objective": "...",
  "scope": { "in": ["..."], "out": ["..."] },
  "constraints": { "hard": ["..."], "prefs": ["..."] },
  "invariants": ["..."],
  "decisions": [{ "id": "D7", "summary": "...", "rationale": "..." }],
  "status": { "done": ["..."], "doing": ["..."], "blocked": ["..."] },
  "next_actions": [{ "id": "A9", "text": "...", "done_when": "..." }]
}
```

---

## 9) Advanced Usage: Agent-Managed Continuity (Automated PMP/ADR Maintenance)

This section describes using an agent to maintain the continuity artifacts automatically, so developers do not manually update PMP/ADR/changelog.

### 9.1 Objectives and Design Principles

Agent-managed SCP should satisfy:

1. **Canonical state outside chat**
2. **Deterministic, reviewable updates**
3. **No silent changes to invariants/decisions**
4. **Token efficiency via minimal context loading**

---

### 9.2 Reference Architecture

Typical components:

- **State store (canonical):** `project_state.json` (authoritative)
- **Human-readable rendering:** `project_memory_pack.md` (optional)
- **Decision store:** `decisions/` directory or a single file
- **Changelog store:** optional `changelog.md`
- **Session orchestrator:** builds prompts, captures outputs, applies validated updates
- **Validation layer:** schema checks and invariant enforcement

---

### 9.3 Two-Phase Update Model (Strongly Recommended)

**Phase A — Work Output**
The agent produces the requested work (design/code/spec). It does not mutate state directly.

**Phase B — State Update Proposal**
The agent produces a structured **patch proposal** describing:

- PMP updates
- new decisions
- changelog entries
- any explicit invariant change request

This enables validation and prevents uncontrolled rewriting of truth.

---

### 9.4 Patch Contract (Update Proposal Format)

Example JSON patch-style proposal:

```json
{
  "pmp_version_bump": true,
  "pmp_updates": [
    {
      "op": "add",
      "path": "/status/done/-",
      "value": "A11: Add request logging middleware"
    },
    {
      "op": "replace",
      "path": "/status/doing",
      "value": ["A12: Add Redis cache layer"]
    },
    {
      "op": "add",
      "path": "/interfaces/api_endpoints/-",
      "value": "GET /v1/metrics"
    }
  ],
  "new_decisions": [
    {
      "id": "D14",
      "date": "2025-12-04",
      "context": "Need caching to reduce DB load under peak traffic.",
      "decision": "Adopt Redis for caching read-heavy endpoints.",
      "alternatives": ["In-memory cache", "Memcached", "DB indexing only"],
      "reasoning": "Redis supports TTL, shared cache across instances, standard ops tooling.",
      "consequences": "Adds Redis dependency; requires cache invalidation strategy.",
      "reversal_conditions": "If operational burden exceeds benefit or hit rate < 20%."
    }
  ],
  "changelog": [
    "Moved A11 to Completed.",
    "Started A12 in progress.",
    "Added endpoint GET /v1/metrics.",
    "Added Decision D14 (Redis caching)."
  ],
  "invariant_change_request": null
}
```

#### Explicit invariant change requests only

Any invariant change must be isolated and explicit:

```json
{
  "invariant_change_request": {
    "invariant_id": "I3",
    "current_text": "Auth token TTL fixed at 15m",
    "proposed_text": "Auth token TTL configurable (default 15m)",
    "reason": "Customer requires variable TTL for enterprise SSO integration",
    "required_decision_id": "D15"
  }
}
```

**Validation rule:** Reject patches that alter invariants unless an `invariant_change_request` is present and a corresponding new decision exists.

---

### 9.5 Prompting Pattern for Agent-Managed Operation

Use explicit role separation:

```text
You must produce two sections:

(1) WORK OUTPUT: do the requested work.

(2) STATE UPDATE PROPOSAL: propose updates to the canonical project state using the patch schema provided.

Rules:
- Do not modify invariants or key decisions silently.
- Any invariant change must be in invariant_change_request and must include a new ADR-lite decision.
- Keep PMP concise; update status/next actions rather than adding narrative.
```

---

### 9.6 Retrieval Strategy (Token Efficiency)

**Always load**

- Objective, scope, constraints, invariants
- Current state (done/doing/blocked)
- Next actions
- Interfaces/contracts

**Retrieve on demand**

- Relevant decision records by topic (auth, DB, frontend)
- Supporting artifacts (spec sections, schemas)

---

### 9.7 Validation and Safety Controls

Minimum controls:

- Schema validation (PMP JSON, patch format)
- Invariant enforcement (“no silent changes”)
- Decision ID uniqueness and referential integrity
- Diff generation and review (especially for invariant changes)

---

## 10) Common Failure Modes and Mitigations

1. **PMP becomes too long**
   Mitigation: enforce size budget; move detail to ADRs and linked artifacts.

2. **Unrecorded rationale causes repeated debates**
   Mitigation: decision log required for non-trivial choices.

3. **Silent constraint changes**
   Mitigation: “No Silent Changes” gate + explicit decision entries.

4. **Vague next actions**
   Mitigation: require “Done when” acceptance criteria.

5. **Agent claims progress it did not actually complete**
   Mitigation: require evidence in “Completed” items (PR link, commit hash, test results), or gate completion behind external checks.

---

## 11) Minimal Checklist (Operational)

### Start of session

- Load latest PMP.
- Confirm constraints and invariants are restated.
- Confirm proposed plan references decision IDs.

### During session

- Create ADR-lite entries for material decisions.
- Update interfaces/contracts in PMP when they change.

### End of session

- Increment PMP version and persist updates.
- Persist new decision entries.
- Write a short changelog.

---

## 12) Adoption Guidance

To deploy SCP across a team:

1. Add PMP and ADR templates to your repo or documentation system.
2. Add a PR checklist line: “If multi-session AI was used, PMP/ADR updated.”
3. Teach the analogy: “Each session is a new hire; give them the onboarding packet.”
4. If using an agent, implement two-phase updates with validation and diff review.

---

## Resources

- **Architecture Decision Records (ADRs) and decision logs:** ([Architectural Decision Records][1])
- **ADR definition and common structure (context/decision/consequences):** ([GitHub][2])
- **Tokens and tokenization (context window mechanics and token budgeting):** ([help.openai.com][3])

[1]: https://adr.github.io/?utm_source=chatgpt.com "Architectural Decision Records"
[2]: https://github.com/joelparkerhenderson/architecture-decision-record?utm_source=chatgpt.com "Architecture decision record (ADR) examples for software ..."
[3]: https://help.openai.com/en/articles/4936856-what-are-tokens-and-how-to-count-them?utm_source=chatgpt.com "What are tokens and how to count them?"

## Glossary

- **Context Window**: The maximum amount of text (tokens) an LLM can consider at once. Older context is truncated when the window is exceeded.
- **Drift**: Gradual divergence of outputs from the project’s established decisions, constraints, and intent, often caused by missing state or silent assumption changes across sessions.
- **Invariant**: A project rule or constraint that must remain stable unless explicitly changed through change control (e.g., “Use PostgreSQL,” “Endpoints are versioned under /v1”).
- **Session Amnesia**: Loss of continuity across sessions where the model lacks prior state and decisions, leading to rework, incorrect assumptions, and misalignment.
- **Tacit (Tacit Knowledge / Tacit Decisions)**: Information that is “known” by participants through experience or prior discussion but is not explicitly documented (e.g., “we rejected option B because ops can’t support it”). Tacit knowledge is a primary driver of drift if not captured in decisions.
- **Token Budget / Token Efficiency**: The practical limit on how much text you can include in prompts and responses; SCP aims to minimize repeated context loading by storing compact state.
- **Two-Phase Update Model**: An agent workflow where the model first produces work output, then separately produces a structured state update proposal that is validated and applied, preventing uncontrolled state mutation.
- **ADR (Architecture Decision Record)**: A formal method of documenting architecture decisions, typically including context, decision, alternatives, and consequences.
- **ADR-lite**: A simplified, lightweight ADR format used to capture essential decision rationale without heavy process; sufficient to prevent re-deciding and preserve intent across sessions.
