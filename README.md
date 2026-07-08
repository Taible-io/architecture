# Taible-io — Architecture (as code)

The **system of record for how Taible is built**. Every diagram is [D2](https://d2lang.com) source, versioned in git. Nothing is hand-exported — CI renders each `.d2` to SVG on every push.

> New to Taible? Read the org onboarding first: **[Taible-io/playbok_startup](https://github.com/Taible-io/playbok_startup)** → `README.md` then `organization-context.md`. This repo is the "second door": the whole system, as code.

**Stage:** 🔧 Early. The diagrams here are first-pass sketches meant to evolve — change them via PR.

---

## Why D2, C4, and CI

- **D2 source of truth** — diagrams are text, so they diff, review, and version like any code. `git log` becomes the evolution of the company's design.
- **[C4 model](https://c4model.com)** — diagrams are organized from big-picture to detail (Context → Container → Component), so they stay navigable as the system grows.
- **CI rendering** — GitHub Actions renders `.d2` → `.svg` reproducibly (pinned D2 version). Source is truth; renders are generated. See [ADR-0002](./adr/0002-d2-c4-and-ci-rendering.md).

---

## Layout

```
architecture/
├── diagrams/
│   ├── context/          # C4 L1 — system in its environment (people + external systems)
│   ├── container/        # C4 L2 — apps, services, datastores and how they talk
│   ├── component/        # C4 L3 — inside a single service (example: ai-model-nlp)
│   ├── data/             # database / data model sketches
│   └── ui/               # UI flows and screen maps
├── rendered/             # SVGs produced by CI (do not edit by hand)
├── adr/                  # Architecture Decision Records (the "why")
└── .github/workflows/    # render-d2.yml (the render pipeline)
```

---

## Add or change a diagram

1. Edit or add a `.d2` file under the right `diagrams/<level>/` folder.
2. Preview locally (optional): install D2, then `d2 diagrams/context/system-context.d2 out.svg`.
3. Open a PR. CI validates that every diagram renders. On merge to `main`, CI writes the SVGs into `rendered/`.
4. If it's a **decision** (a tradeoff, not just a drawing), add an ADR — see [`adr/`](./adr).

Install D2 locally:
```bash
curl -fsSL https://d2lang.com/install.sh | sh -s --
```

---

## Diagrams today

| File | Level | What it shows |
|---|---|---|
| `diagrams/context/system-context.d2` | C4 L1 | Diners, restaurant partners, and the external systems around Taible. |
| `diagrams/container/containers.d2` | C4 L2 | Mobile app, API gateway, the three services, and the database. |
| `diagrams/component/ai-model-nlp-components.d2` | C4 L3 | Inside the AI/NLP service (example component view). |
| `diagrams/data/database.d2` | Data | First-pass data model (users, restaurants, orders, payments). |
| `diagrams/ui/ui-flows.d2` | UI | Core mobile ordering flow. |

These map to the service repos: [`ai-model-nlp`](https://github.com/Taible-io/ai-model-nlp), [`mobile-app`](https://github.com/Taible-io/mobile-app), [`crypto-payments`](https://github.com/Taible-io/crypto-payments), [`restaurant-partnerships`](https://github.com/Taible-io/restaurant-partnerships).

---

*Part of [Taible-io](https://github.com/Taible-io). Owner: caeltarifa. Never commit secrets.*
