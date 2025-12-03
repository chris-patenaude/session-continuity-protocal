## Drop-in `HANDOFF.md` template (SCP, domain-agnostic)

```md
# HANDOFF.md (SCP)

## 0) SCP Rules (apply in every session)

- Treat this file as the authoritative continuity artifact for the next session.
- Do not “reconstruct context” by asking to reread everything; use this handoff as your source of truth.
- Keep this handoff concrete and compact: decisions, invariants, interfaces/definitions, run instructions, current status, next steps.
- Roadmap adherence: execute only the deliverables for the Current session unless the roadmap authorizes pulling work forward.
- End of session: update this file and output it in full.

## 1) Project Roadmap (SCP session plan)

**Current session:** S<k>
**Roadmap status:** <On track | At risk | Blocked>

### S1 — <Session name>

- **Goal:** <one sentence>
- **Prereqs:** <bullets>
- **Deliverables:** <bullets: artifacts/files/decisions>
- **Acceptance checks:** <bullets: validations/tests/review criteria>
- **Out of scope:** <bullets>
- **Notes/Risks:** <optional>

### S2 — <Session name>

- **Goal:**
- **Prereqs:**
- **Deliverables:**
- **Acceptance checks:**
- **Out of scope:**
- **Notes/Risks:**

### S3 — <Session name>

- ...

## 2) Snapshot (current truth in <60 seconds)

**Objective:** <what we are currently trying to achieve>
**Scope boundary:** <what is explicitly in-scope / out-of-scope for the Current session>
**Current status:** <what works / what’s broken / what’s blocked>
**Key artifacts:** <links/paths/names of the few most important things>
**How to validate:** <the single best “proof it works” check>

## 3) Current State (what exists right now)

- <main capabilities / deliverables that already exist>
- <known gaps>

## 4) Interfaces / Definitions / Contracts (copy exact forms)

- <API signatures, schemas, domain definitions, acceptance criteria>
- <what must remain compatible>

## 5) Decisions & Invariants (do not silently change)

- **Decisions made:** <tradeoffs, chosen approach, constraints agreed>
- **Invariants:** <things that must remain true>
- **Open questions:** <what still needs a decision; how to decide>

## 6) Work Log (since last session)

- <what changed, at a high level, and where>

## 7) Next Session Kickoff (copy/paste tasking)

**First actions (do these first):**

1. <step 1>
2. <step 2>
3. <step 3>

**Primary deliverable for next session:**

- <what “done” means next session>

**Risks / pitfalls to avoid:**

- <failure modes, edge cases, constraints>
```
