# Session Continuity Protocol (SCP)

**Session Continuity Protocol (SCP)** is a lightweight, repeatable method for preventing "session amnesia" when using large language models across multiple chat sessions on the same project.

It treats each new session as equivalent to onboarding a capable new developer who has not attended prior meetings.

## Quick Links

*   [**Read the Full Specification**](../README.md)
*   [**Templates**](../templates/)
*   [**Prompts**](../prompts/)
*   [**Examples**](../examples/)

## At a Glance

*   **Core Principle:** Chat history is not state. State must be explicit, versioned, and reloadable.
*   **Required Artifacts:** Project Memory Pack (PMP), Decision Log (ADR-lite).
*   **Key Rule:** "No Silent Changes" — Invariants and decisions must not change without explicit documentation.

## Quickstart (15 minutes)

1.  **Setup:** Copy `templates/project_memory_pack.md` into your project.
2.  **Start Session:** Copy/paste `prompts/bootstrap_prompt.txt`.
3.  **End Session:** Copy/paste `prompts/closeout_prompt.txt` and update state.

See the [README](../README.md) for the full protocol text.

