# Hardware-offer index: plan

## What we are building

An index of hardware offers that a person can look up and read:

> This is X; it cost Y on date Z.

- **X** is the item and how it is configured.
- **Y** is the price and what that price covers.
- **Z** is when the offer was seen.

Everything in a result comes from what was captured from the listing. A parser or a pile of extracted fields is not the product. The readable result is.

## The whole project, in proportion

1. **Supply (small).** One collector producing fresh captures. It exists to unblock everything else with real data, real timestamps and real prices.
2. **Interpretation (the core).** A model reads a capture and says what the item is, how it is configured, what the price is and what the price covers. The previous hand-tuned parser failed here, because listing wording never stops varying. This is the main thing being built.
3. **Answer key and routine model.** Frontier models produce the reference answers. Cheaper models are then run through the same setup and measured against that reference, to find the cheapest one that holds up day to day.
4. **The index itself.** Readings are linked to a hardware identity so offers for the same thing sit together, and repeated sightings of an offer become price history. This is the actual product, and the least defined part of the project.
5. **Routine operation.** It runs on a schedule across more sources, with cost and failures visible, and results are re-run when the prompt or model changes.

Most of the design thinking belongs in part 4. It is deferred until parts 1 to 3 are producing readings to design against.

## How we start

Stand up **one** existing collector from the TechdealsHandoff repo (`C:\_projects\TechdealsHandoff`).

- Use it as it exists. Fix only what stops it from producing captures.
- "Working" means: run on demand, it writes a few hundred listings, each with the raw payload, the listing URL, the fetch time, and the price and currency as displayed.
- Scheduling, retries and deduplication come later.
- A second source waits until the first runs end to end through parts 2 and 3.

## The role of the frontier model

- **It is the answer key.** It replaces human labeling. Its reading of a capture is the reference, and nobody adjudicates it row by row.
- **It is the control for the setup.** If a frontier model gets a listing wrong, the prompt, the output contract or the capture is at fault, and that is what gets fixed.
- **It is construction-time only.** It is too expensive to run routinely, so the question the project answers is how cheap a model can match it through the same setup.

Two frontier models agreeing is treated as correct. Disagreements between them are the only rows worth looking at, and they usually mark listings that really are ambiguous.

## Working rules

- **Models read only what the capture contains.** If something is not in the capture, it is a question of whether the collector should capture it, never something to ask a model.
- **Reuse what exists.** TechdealsHandoff has a lot of usable code. Nothing gets ported wholesale or rewritten for its own sake.
- **Keep the plan general.** It is not built around particular fixtures or old records.
- **Keep it small.** No added scope, no extra checks layered on for reassurance.

## Open questions

These are answered once real readings exist, not before.

- What "X" links to, and at what level of detail (a model line, or a specific configuration).
- Whether one record is a capture, an offer or a variant of an offer.
- How repeated sightings, changed prices and relistings become price history.
- Whether the model that reads listings needs any tools, or a single structured call is enough.

## Status (2026-11-26)

- Repo created.
- A read-only survey of the existing collectors in TechdealsHandoff is in progress. None has been chosen or run yet.
- This replaces an earlier, longer draft. The order of work is the same. The difference is that frontier output is trusted as the reference, and the hedges and prohibitions of that draft are not requirements.
