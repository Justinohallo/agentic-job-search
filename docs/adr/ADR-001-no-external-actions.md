# ADR-001 — No agent acts externally without a person

**Status:** Accepted, 2026-11-04 · **Applies to:** every milestone

## Decision

No agent in this system sends an email, submits an application, posts a message or contacts a person. The highest autonomy level any agent gets is L3: acting on internal state only. External actions are always taken by the user.

## Why

Applying for a job and talking to a recruiter can't be undone, and the cost of a bad one is paid in reputation, which no metric in the system tracks. Job postings are also untrusted text fed into prompts, so an injected instruction must never be able to reach an action with outside effects.

## Whiteboard proof

The autonomy table (L0 human → L4 unattended external action) with every arrow that leaves the system passing through the user. Then point at the code: no module except `judge.ts` makes a network call, and it only reads.

## What would change it

Nothing in this project. A future system with a narrow, reversible action (for example, adding a calendar hold the user can delete) would need its own ADR.
