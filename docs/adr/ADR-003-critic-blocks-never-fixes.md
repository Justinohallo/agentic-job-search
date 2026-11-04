# ADR-003 — The critic can block, never fix

**Status:** Accepted, 2026-11-04 · **Applies to:** `ground` (M1) and every later reviewer agent

## Decision

`ground` reads its inputs and reports findings. It never edits a resume, master or brief, and it never suggests replacement text.

## Why

A critic that rewrites failing lines becomes a second, unaudited author: the thing checking the claims would also be making them. Keeping it read-only means every word that ships was written by the user or the tailor and checked by the critic. TC-11 enforces this by hashing the fixtures before and after a run.

## Whiteboard proof

Two boxes, author and critic, with one arrow from author to critic labelled "draft" and one arrow back labelled "findings". There's no arrow from the critic to the document.

## What would change it

Nothing for `ground`. A separate "repair" agent could exist later, but its output would go back through `ground` like any other draft.
