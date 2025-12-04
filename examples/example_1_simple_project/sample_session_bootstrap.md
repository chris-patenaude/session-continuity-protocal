# Sample Session Bootstrap

**User Input:**

```text
You are continuing an ongoing project. Treat the Project Memory Pack below as the source of truth.

Rules:
1) Do not change any Invariants or Key Decisions unless you explicitly propose a new Decision record explaining why.
2) If something is missing, state it as an assumption and proceed with the safest default consistent with the Memory Pack.
3) Start by producing: (a) constraints+invariants restatement, (b) plan tied to decisions, (c) first concrete step.

<Project Memory Pack>
... [Content of pmp_v1.md] ...
</Project Memory Pack>
```

**Model Response:**

```text
I have reviewed the Project Memory Pack v1.

### 1. Constraints & Invariants Restatement
- **Hard Constraints:** Python 3.10+, local files only (no external DB).
- **Invariants:**
  - I1: Data must be human-readable (JSON/CSV).
  - I2: Zero 3rd party API dependencies.
- **Key Decision:** D1 establishes we are using JSON for storage.

### 2. Plan
My focus for this session is to clear the initial actions:
1.  Initialize the project with `typer` (matches preference).
2.  Implement the JSON storage handler (adhering to D1).
3.  Implement `add` command (Action A2).

### 3. First Concrete Step
I will start by creating the file structure and the basic `main.py` using `typer`.

File: `main.py`
...
```
