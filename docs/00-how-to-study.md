# How to Study This Roadmap

System design skill comes from making and defending decisions. Reading is useful, but each module ends with an artifact because recall and design practice expose gaps faster.

## Before each module

- Set a time box and target completion date.
- Skim the learning outcomes and checkpoint questions.
- Write what you already know; do not copy definitions.

## During study

For every concept, capture four things: what it is, when it helps, what it costs, and how it fails. When two approaches overlap, create a trade-off table rather than two isolated definitions.

## After study

1. Complete the exercise from a blank page.
2. Explain it aloud as if an interviewer challenged each choice.
3. Answer the checkpoint questions without notes.
4. Mark gaps and schedule spaced reviews in [PROGRESS.md](../PROGRESS.md).

## Estimation cheat sheet

- Average requests/second ≈ daily requests ÷ 86,400.
- Peak load is often modeled as 2–10× average; state your assumption.
- Storage ≈ records/day × bytes/record × retention × replication factor.
- Bandwidth ≈ requests/second × average payload size.
- Start with powers of ten and refine only when a decision depends on it.

## What “good” looks like

A strong design is internally consistent, explicit about assumptions, and honest about its trade-offs. It does not need every fashionable technology. Prefer the simplest design that meets the stated requirements and has a credible growth path.

[← Repository home](../README.md) · [Start Module 01 →](01-system-design-fundamentals.md)
