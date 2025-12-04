# Contributing to Session Continuity Protocol (SCP)

Thank you for your interest in contributing to SCP! We want to make this protocol as robust and easy to adopt as possible.

## How to Propose Changes

1.  **Issues First:** Please open an issue or discussion before submitting a PR for significant changes.
2.  **Fork & Branch:** Fork the repo and create a branch for your feature or fix.
3.  **Pull Request:** Submit a PR referencing the issue.

## The "No Silent Changes" Rule

Just as SCP enforces "no silent changes" for projects, we apply it to the protocol itself.

- **Invariants & Terminology:** If you propose changing a core definition (e.g., what an "Invariant" is), you must explicitly call this out in your PR description.
- **Stability:** We aim to keep the core templates (PMP, ADR-lite) stable so adopters don't have to constantly refactor their workflow.

## Style Guide

- **Markdown:** Keep it standard GitHub Flavored Markdown.
- **Templates:** Changes to `templates/` should be reflected in the `README.md` examples if applicable.
- **Version Bump:** If you change the spec in a way that alters behavior, please suggest a version bump in your PR.

## PR Checklist

- [ ] Documentation updated (README.md, etc.)
- [ ] Examples updated (if behavior changes)
- [ ] Changelog updated
