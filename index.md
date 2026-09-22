---
title: The gNVC card format
description: An open, hand-writable format for Nonviolent Communication cards. One card, one YAML file, readable without any software.
---

# The gNVC card format

Version 1.0. Permanent home: [`https://w3id.org/gnvc/1.0`](https://w3id.org/gnvc/1.0).

A gNVC card is one moment put into words: what happened, what the writer felt, the need
underneath, and what they would like to ask. It is a single YAML file that a person can read
top to bottom without any software at all.


**gNVC** (giraffe-language NVC card format, after the giraffe that Nonviolent Communication takes as its emblem) is a deliberately small, versioned, hand-writable format. Design goals: **readable top-to-bottom by a human with no app** (field order puts who-and-what first, machine bookkeeping last); writable by hand in any text editor; parseable by any YAML 1.2 library; validatable against a published schema (see *Publishing*); forward-compatible (unknown keys are preserved on merge and round-trip).

**One document per file. One document kind: the card.** Responses are not a second format — they are new `status_history` entries on the card itself (see *A plain response*). Entanglement between two cards is a plain `id` pointer, carrying no state (see *An entangled response*).

## Example — a request card

```yaml
# A Giraffy card — written with care. Read it top to bottom,
# or open it at https://giraffy.riverma.com

from: Maya
to: Sam
about: "Last night's dinner dishes"

summary: >
  When I saw the dishes from last night still on the counter this
  morning, I felt frustrated and a little discouraged, because I
  need shared care for our home and reliability around agreements.
  Would you be willing to wash your dishes before bed tonight?

observation: >
  When I saw the dishes from last night still on the counter
  this morning.

feelings:
  - frustrated
  - discouraged

needs:
  - shared care for our home
  - reliability around agreements

requests:
  - Would you be willing to wash your dishes before bed tonight?

status: shared
status_history:
  - { state: ready,  by: Maya, at: "2026-07-10T18:20:00Z" }
  - { state: shared, by: Maya, at: "2026-07-10T18:22:00Z" }

entangled_with: []             # pointers to other cards, by id

# Optional precise mappings. Indices are 0-based into the lists above.
# Omitted = "everything relates to everything".
mapping:
  feelings_to_needs:
    - [0, 1]                   # frustrated ↔ reliability around agreements
    - [1, 0]                   # discouraged ↔ shared care for our home
  requests_to_needs:
    - [0, 0]
    - [0, 1]

# ---- app-only details ----
kind: request                  # request | gratitude
id: 7f3b9e2c-4a1d-4e08-9c11-52d8a0b6f3aa   # minted once, never changes
created: "2026-07-10T18:04:00Z"
updated: "2026-07-10T18:22:00Z"
gnvc: "1.0"  # spec: https://w3id.org/gnvc/1.0
```

*(This is the shape a client should emit: human-first order, the app-only details gathered under a divider near the end, and the `gnvc: "1.0"  # spec: …` version line last. `mapping` is optional.)*

## Example — a plain response (the same card, coming back)

Sam first sends **Heard** (with a reflection), then, after sitting with it, an honest **I cannot**. **No new file format** — he shares Maya's card back with its history extended (everything else unchanged):

```yaml
# …same card as 6.1, with status and history updated…

status: no
status_history:
  - { state: ready,    by: Maya, at: "2026-07-10T18:20:00Z" }
  - { state: shared,   by: Maya, at: "2026-07-10T18:22:00Z" }
  - state: heard
    by: Sam
    at: "2026-07-10T21:05:00Z"
    note: >
      It sounds like you're feeling frustrated and discouraged
      because you need shared care and reliability around agreements.
  - state: no
    by: Sam
    at: "2026-07-11T08:10:00Z"
    note: >
      Saying yes tonight would set aside my need for rest — I'm
      wiped out after closing shifts. I'm glad you told me this.
```

On import, Maya's app merges by card `id`: the two new history entries are unioned in, her card's status becomes `no`, and nothing else changes.

## Example — an entangled response (`entangled_with` is a plain pointer)

Sam instead answers **Let's explore** and surfaces the need of his that's tangled up in Maya's request. He writes **his own card**, which simply names Maya's card by `id` in its `entangled_with` list. **The link carries no state lineage** — it is just a pointer (with an optional human comment). Sam's own response history stays in *his* card; Maya's card, marked `maybe` and shared back separately (as in *A plain response*), carries the `heard`/`maybe` entries in *its* history.

```yaml
# A Giraffy card — written with care.

from: Sam
to: Maya
about: "Evenings after closing shifts"

summary: >
  When I get home after closing the store at 10pm, I'm exhausted and
  depleted, because I need rest and some ease in my evenings. Would
  you be willing to try dishes-in-the-morning on my closing nights?

observation: >
  When I get home after closing the store at 10pm on weeknights.

feelings: [exhausted, depleted]

needs: [rest, ease in the evenings]

requests:
  - Would you be willing to try dishes-in-the-morning on my closing nights?

status: shared
status_history:
  - { state: shared, by: Sam, at: "2026-07-10T21:40:00Z" }

# names one of Maya's cards as entangled — just a pointer:
entangled_with:
  - id: 7f3b9e2c-4a1d-4e08-9c11-52d8a0b6f3aa   # Maya's dishes request

# ---- app-only details ----
kind: request
id: 91c4d7aa-2e0b-4f7e-8d3c-6b1f0a9e5c22
created: "2026-07-10T21:38:00Z"
updated: "2026-07-10T21:40:00Z"
gnvc: "1.0"  # spec: https://w3id.org/gnvc/1.0
```

When Maya imports this file, her app adds Sam's card to her Received list and, seeing his `entangled_with` pointer to a card she holds, **forms the reciprocal link** so the thread appears from either end. An Explore therefore involves up to two ordinary files — Maya's original coming back with a `maybe` in its history (see *A plain response*), and Sam's new card above — each a standalone, valid gNVC card. Nothing carries a special lineage payload; the entanglement pointer plus each card's own history are enough.

## Schema rules

- **`kind` values:** `request`, `gratitude`. One card per file. (There is no `response` kind.)
- **Required keys:** `gnvc`, `kind`, `id`, `from`, `created`, `updated`, `observation`, `feelings`, `needs`; `requests` required for `kind: request`, absent on `gratitude`. `to`, `about`, `summary`, `status`, `status_history`, `entangled_with`, `mapping` optional.
- **Field order is a convention, not a requirement** — parsers accept any order; writers (the app, and hand-authors who care) should emit the human-first order shown above: `from` / `to` / `about`, then `summary`, then the four NVC parts, then status, links, mapping, and the app-only details last.
- **Identity is the display name.** `from`/`to` are plain names. No profile ids or hidden identifiers; disambiguating people is the receiving user's act, supported by the UI (the client's own people list).
- **State record shape (uniform):** each `status_history` entry is `{state, by, at, note?}`. Valid `state` values are exactly `draft, ready, shared, received, heard, yes, no, maybe, given, celebrated, withdrawn` (the schema enforces this). `draft` may legitimately travel in a shared file: an unfinished card can be sent to be read or added to, and a client distinguishes a draft written *about* someone from one written *by* them using the `by` of the card's `draft` entry.
- **Links:** `entangled_with` is a list of `{id}` pointers (an optional trailing `# comment` is for humans only) referencing direct neighbors. Links carry **no** `from`/`at`/`note`/`states` — a card's states live only in its own `status_history`. Apps form the reciprocal link on import and reconstruct chains at render time; the chain is never serialized.
- **Feelings/needs/requests are plain strings**, not enum-constrained — any vocabulary a client offers is a convenience of that client, not format restrictions. Hand-authors can write anything.
- **Timestamps:** ISO-8601 UTC, and always **strings**. Quote them. YAML 1.2 leaves an
  unquoted `2026-07-10T18:22:00Z` as a string, but parsers that follow YAML 1.1 resolve it
  to a date, and the card then fails validation through no fault of its author. Apps display in local time.
- **Merging:** union `status_history` by `(state, by, at)`; card body fields take the newest `updated` *from the original author only*; unknown keys are preserved verbatim (forward compatibility).
- **Versioning:** `gnvc: "1.0"`. Apps must accept any `1.x` and ignore unknown keys; a future `2.x` may break.
- **File naming convention (non-normative):** `{author}-{kind}-{YYYY-MM-DD}-{first-6-of-id}.gnvc.yaml`.
- **Privacy rule:** private `notes` fields — and the entire needs inventory of a client's private storage (tiers, category overrides, who-might-help intentions, tier history) — are NOT part of this format and are never serialized into shared files (backups use a superset format with an explicit `x-private:` section).

## Publishing

YAML has no native schema language, but **JSON Schema validates YAML directly**: YAML 1.2
parses to the same data model as JSON, and this is the established convention. So one
artifact serves everyone, [`gnvc-card.schema.json`](gnvc-card.schema.json), in JSON Schema
draft 2020-12.

Where it lives, so the format outlives any one host:

1. **Canonical, permanent identifier: `https://w3id.org/gnvc/1.0`.** w3id.org is the
   community-run permanent-URL service used widely for open vocabularies and standards. It
   redirects to wherever the format is actually hosted, so if this repository ever moves,
   the redirect is updated and every card ever written still points somewhere real.
   `.../1.0` serves this page; `.../1.0/schema.json` serves the schema.
2. **Every card cites the format.** The version line carries the permanent URL as a comment,
   `gnvc: "1.0" # spec: https://w3id.org/gnvc/1.0`, so a card found anywhere leads back to
   its own definition. A hand-author who wants live validation in an editor can add the
   standard modeline at the top of a file:
   `# yaml-language-server: $schema=https://w3id.org/gnvc/1.0/schema.json`.
3. **The format is not the client.** Giraffy is one implementation and has no special
   standing here. Anything that reads and writes these files is a gNVC client.

## Licence

The format, the schema and the templates are released under
[CC0-1.0](LICENSE): no rights reserved. Write cards by hand, or teach any program to read
and write them, without asking anyone's permission.
