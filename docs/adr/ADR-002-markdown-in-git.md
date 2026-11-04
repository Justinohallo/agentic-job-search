# ADR-002 — State lives in markdown files in git

**Status:** Accepted, 2026-11-04

## Decision

All state (career master, pipeline, applications, briefs) is plain markdown in a git repository. There is no database.

## Why

The user reads and edits these files directly, so they have to stay human-first. Git gives an audit log, diffs, review and rollback for free. At under 50 applications, a database adds queryability nobody needs and a second source of truth.

## Whiteboard proof

`git log -p career/MASTER.md`: every fact change, who made it and when. Compare with what a database would need to give the same history.

## What would change it

More than about 50 active pipeline rows, or a need to query across applications that grep can't answer.
