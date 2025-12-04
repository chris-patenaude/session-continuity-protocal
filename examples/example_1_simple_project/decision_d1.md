DECISION D1 (2023-10-01)
Context: We need a local storage format for the Todo app.
Decision: Use JSON file (`tasks.json`).
Alternatives considered: SQLite, CSV, Pickle.
Reasoning: JSON is human-readable, easy to diff, and supported natively by Python. SQLite is overkill for this scope.
Consequences / tradeoffs: Performance might suffer if list grows > 10k items (acceptable for this scope).
Reversal conditions: If we need complex querying or handle > 10k items.
