# gNVC — an open card format for Nonviolent Communication

A gNVC card is one moment put into words: what happened, what the writer felt, the need
underneath, and what they would like to ask. It is a single YAML file that a person can read
top to bottom without any software at all.

**The format: [w3id.org/gnvc/1.0](https://w3id.org/gnvc/1.0)**

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

## What is here

| File | What it is |
|---|---|
| [`index.md`](index.md) | The format, in full. Published at [w3id.org/gnvc/1.0](https://w3id.org/gnvc/1.0). |
| [`gnvc-card.schema.json`](gnvc-card.schema.json) | JSON Schema (draft 2020-12) for one card. |
| [`templates/request.gnvc.yaml`](templates/request.gnvc.yaml) | A blank request card, commented, ready to fill in. |
| [`templates/gratitude.gnvc.yaml`](templates/gratitude.gnvc.yaml) | A blank gratitude card. |

## Validating a card

YAML 1.2 parses to the same data model as JSON, so the JSON Schema validates a card directly:

```sh
npx ajv-cli@5 validate --spec=draft2020 -s gnvc-card.schema.json -d your-card.gnvc.yaml
```

For live validation while hand-writing a card, add the modeline at the top of the file:

```yaml
# yaml-language-server: $schema=https://w3id.org/gnvc/1.0/schema.json
```

## Clients

Anything that reads and writes these files is a gNVC client. The format has no privileged
implementation.

- [Giraffy](https://giraffy.riverma.com) — an offline Nonviolent Communication companion
  ([source](https://github.com/riverma/giraffy))

If you build one, open a pull request and add it here.

## Licence

[CC0-1.0](LICENSE). No rights reserved. Write cards by hand, or teach any program to read and
write them, without asking anyone's permission.
