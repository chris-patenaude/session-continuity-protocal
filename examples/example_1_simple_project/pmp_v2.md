PROJECT MEMORY PACK v2 (Last updated: 2023-10-02)

1. Objective

- Build a simple CLI Todo Application to demonstrate SCP.

2. Scope

- In scope: Add, list, complete tasks. Command line interface.
- Out of scope: User accounts, cloud sync, GUI.

3. Constraints

- Hard constraints: Use Python 3.10+. No external database servers (local files only).
- Preferences: Use `typer` or `click` for CLI.

4. Invariants (must not change without explicit decision)

- I1: Data must be stored in a human-readable format (JSON/CSV).
- I2: Zero 3rd party API dependencies.

5. Key Decisions (with rationale)

- D1: JSON Storage — because it is native to Python and easy to debug.

6. Current State

- Completed:
  - A1: Create project structure and hello world CLI.
  - A2: Implement `add` command.
- In progress:
  - A3: Implement `list` command.
- Blocked: None.

7. Interfaces / Artifacts (source of truth)

- Data schemas:
  - Task: { id: int, title: str, status: "pending"|"done" }
- CLI Commands:
  - `add <title>`
  - `list` (pending)

8. Known Issues / Risks

- Concurrent file access might be tricky.

9. Open Questions (owner + target date if applicable)

- None.

10. Next Actions (ordered, each with acceptance criteria)

- A3: Implement `list` command. (Done when: `python main.py list` shows tasks).
- A4: Implement `complete` command. (Done when: `python main.py complete <id>` changes status).
