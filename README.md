# Session Chaining Protocal (SCP)

Continuity for multi-session AI work using a single handoff artifact (`HANDOFF.md`)

---

## 1) What SCP is

**Session Chaining Protocal (SCP)** is a continuity method for splitting large or complex AI tasks across multiple chat sessions without losing state or wasting the new session’s context window. SCP relies on a single machine-oriented handoff artifact, `HANDOFF.md`, that carries only the information required to resume work accurately: global operating rules, a compact project snapshot, current state, key decisions and invariants, a session-by-session roadmap, and explicit kickoff instructions for the next session. At the end of each session, the agent updates and outputs the full `HANDOFF.md`, enabling the next session to continue without rereading the entire project or re-deriving assumptions.

---

## 2) The problem SCP solves

Large or complex tasks can exceed a single session’s context window. Splitting work into multiple sessions introduces “session amnesia”:

- The new session lacks prior decisions and state, so it may make incorrect assumptions.
- Reloading the full project context consumes the new session’s budget and still misses tacit decisions.
- The result is drift, rework, and token inefficiency.

SCP addresses this by **treating continuity as a first-class deliverable**.

---

## 3) Core mechanism: the continuity artifact (`HANDOFF.md`)

SCP uses **one** document as an AI-to-AI state carrier:

- **File name:** `HANDOFF.md`
- **Purpose:** machine-oriented continuity (not human documentation)
- **Contents:** only “decision-critical” and “execution-critical” context:
  - current objective and scope boundaries
  - what exists and what is broken/blocked
  - key interfaces/contracts (exact signatures), definitions, and invariants
  - minimal repo/artifact map (only what matters next)
  - how to run/validate, test status, recurring pitfalls
  - **project roadmap** broken into sessions (big picture)
  - kickoff instructions for the next session (first actions, deliverable, pitfalls)

SCP also recommends maintaining a separate human-facing doc (`README.md` or equivalent) for onboarding and usage.

---

## 4) SCP Contract (hard rules)

These rules are the “protocol law” that makes SCP reliable across sessions:

1. **Single source of continuity truth:** Maintain one `HANDOFF.md` (no competing summaries).
2. **Concrete, compact writing:** Prefer exact paths, commands, signatures, acceptance checks. Avoid narration.
3. **Roadmap adherence:** Execute only the deliverables for the **Current session** unless the roadmap explicitly authorizes pulling work forward.
4. **End-of-session requirements:**
   - Update `HANDOFF.md` and output it **in full** for the next session.
   - Update the human-facing `README.md` (or equivalent) when user-facing behavior changes.

---

## 5) SCP lifecycle (how sessions chain)

### Start of a session (inputs)

Operator provides:

1. global instructions (optional but helpful),
2. the latest `HANDOFF.md`,
3. the scoped task prompt for this session (often derived from the roadmap).

The agent should treat `HANDOFF.md` as the source of truth and avoid requesting full project context unless explicitly required.

### During the session (execution)

- Implement only what the session scope demands.
- Keep `HANDOFF.md` synchronized as decisions, interfaces, or key state change.

### End of the session (outputs)

- Emit the full updated `HANDOFF.md`.
- Update `README.md` (or analogous human-facing docs) if needed.

---

## 6) Why SCP works (token economics)

SCP replaces “load everything” with “load the minimal state vector”:

- It preserves continuity by explicitly carrying decisions, invariants, and current truth.
- It reduces drift by providing a roadmap and explicit scope boundaries.
- It increases efficiency by minimizing onboarding tokens per session and maximizing productive tokens.

---

## 7) Beyond coding: where SCP generalizes

SCP works anywhere tasks span sessions and correctness depends on prior decisions:

- Research synthesis: hypotheses, sources, unresolved questions, next experiments.
- Product/PRDs: decisions, constraints, acceptance criteria, outstanding stakeholder inputs.
- Legal drafting: definitions, cited clauses, issue list, next redlines.
- GTM planning: positioning, channel experiments, metrics, next sprint.

The only change is the nature of “interfaces” (e.g., definitions, decisions, acceptance criteria) and the validation method.

---

## 8) Recommended `HANDOFF.md` structure (order matters)

Put intent and scope before details:

1. **SCP Rules**
2. **Project Roadmap (session plan + Current session marker)**
3. Snapshot (current truth in <60 seconds)
4. Current State (what exists)
5. Interfaces / Definitions / Contracts (copy exact forms)
6. Decisions & Invariants (do not silently change)
7. Work Log (what changed since last session)
8. Next Session Kickoff (copy/paste tasking to start the next session)

See, the [Handoff Template](./HANDOFF_TEMPLATE.md) for quick reference.

## 9) Optional: “SCP Kickoff” snippet for any new session

Paste this at the top of a new session, along with the current `HANDOFF.md`:

```md
1. Read HANDOFF.md and treat it as the source of truth.
2. Execute only the scoped task in “Current session”.
3. Before ending, update HANDOFF.md and output it in full.
```

---

## 10) Guidance on writing a good roadmap (so the agent sees the big picture)

A roadmap should prevent drift while staying token-efficient. Each session entry should include:

- **Goal:** one sentence that defines why the session exists.
- **Prereqs:** what must already be true to start safely.
- **Deliverables:** artifacts the agent must produce (files, decisions, outputs).
- **Acceptance checks:** how success is verified.
- **Out of scope:** explicit guardrails.
- **Risks/notes:** only if they materially reduce mistakes.

A “Current session” marker (e.g., `S3`) is essential so the agent immediately knows where it is in the plan.

---

## 11) Operational checklist (for adopting SCP)

Before ending a session, ensure `HANDOFF.md` answers:

- What is the **Current session** and what deliverables are required by the roadmap?
- What is true **right now** (status, what works, what’s failing)?
- What must not change (interfaces/definitions/invariants)?
- What files/artifacts matter next (minimal map, not everything)?
- What are the first 3 actions for the next session?
- What is the acceptance check that proves the next session completed its deliverable?
