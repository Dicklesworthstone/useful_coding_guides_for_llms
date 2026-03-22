# Changelog

All notable changes to **useful_coding_guides_for_llms** are documented here.

This repository has no formal release tags or GitHub releases. Changes are
organized below by capability area rather than raw diff order, so readers can
quickly find what matters to them. Every link points to the live commit on
GitHub.

Repository: <https://github.com/Dicklesworthstone/useful_coding_guides_for_llms>

---

## Next.js 15 Best Practices Reference

The core value of this repository: a production-grade architectural guide for
Next.js 15, the App Router, and Zustand, paired with a checklist that tracks
real-world adoption.

### Best Practices Guide

- **`NEXTJS15_BEST_PRACTICES.md`** (961 lines) -- the primary reference
  document covering six major areas plus an advanced addendum:
  1. Foundational architecture and file organization (scalable `src` layout,
     colocated route logic, Partial Pre-rendering configuration)
  2. Server-first data paradigm (async Server Components, caching semantics
     changed in Next.js 15, React 19 `use()` integration)
  3. Data mutations via Server Actions (progressive-enhancement forms,
     `useActionState`, optimistic UI with `useOptimistic`, security hardening)
  4. Zustand for client-side UI state (per-request store factories,
     `StoreInitializer` pattern, `useShallow` selector optimization, Immer
     middleware, advanced devtools patterns)
  5. Secure authentication with `httpOnly` cookies, Server Actions, and edge
     middleware (including CVE-2025-29927 notes on `x-middleware-subrequest`)
  6. End-to-end type safety with OpenAPI client generation (`openapi-ts`)
  7. Addendum: `unstable_cache` with tag-based revalidation, `after()` API for
     post-response work, client router cache `staleTimes`, observability,
     Edge Runtime, React Compiler auto-memoization, scalable component
     architecture (Atomic Design + feature slices)

  Added 2025-06-26.
  ([`230cef5`](https://github.com/Dicklesworthstone/useful_coding_guides_for_llms/commit/230cef55ec269c96642ee30125a4050005a3a10f))

### Implementation Progress Tracker

- **`NEXT_BEST_PRACTICES_IMPLEMENTATION_PROGRESS.md`** (322 lines) -- a
  per-section checklist that maps every recommendation in the guide to an
  implementation status in a real codebase. Includes percentage-complete
  annotations, dated completion stamps, and priority actions for remaining
  work.

  Added in the same commit as the guide.
  ([`230cef5`](https://github.com/Dicklesworthstone/useful_coding_guides_for_llms/commit/230cef55ec269c96642ee30125a4050005a3a10f))

---

## Prompt Engineering Methodology

The research methodology behind the guide: a seed prompt that orchestrates
multiple LLMs to scour the web for current best practices, then merge and
iteratively refine them into a single authoritative document.

- **`PROMPT_TO_GENERATE_AND_REVISE_GUIDES.md`** (1 119 lines) -- contains the
  full prompt used to direct Claude Opus, GPT-4o, and Gemini Deep Research
  through a multi-model research loop. Embeds an earlier draft of the best
  practices guide so each model can propose targeted revisions. Designed to be
  re-run periodically as the Next.js ecosystem evolves.

  Added 2025-06-26.
  ([`44fee35`](https://github.com/Dicklesworthstone/useful_coding_guides_for_llms/commit/44fee3535d6082ad2dd267442a0d57c21363288f))

---

## Agentic Coding Workflow

Guides and templates for using AI coding assistants (particularly Claude Code)
to systematically apply best practices across a codebase.

### Claude Code Transcript

- **`EXAMPLE_USING_THE_TECHNIQUE_IN_CLAUDE_CODE.md`** (1 948 lines) -- a
  complete, unedited transcript demonstrating the iterative workflow: reading
  the best-practices guide, updating the progress tracker, and implementing
  patterns such as `unstable_cache` integration, route colocalization, and
  caching strategy adoption. Shows how step-by-step requests and careful
  progress updates keep the assistant grounded.

  Added 2025-06-26.
  ([`06c6cd9`](https://github.com/Dicklesworthstone/useful_coding_guides_for_llms/commit/06c6cd94385bcd6c9f2e3e8aa627603611ae6993))

### Bug Fixing Template

- **`BUG_FIXING_TEMPLATE.md`** (26 lines) -- a template prompt for directing
  agentic coding assistants to systematically fix type-check and linter errors
  from a combined output file. Key techniques:
  - Random chunking of error lines for multi-agent parallelism
  - `[COMPLETED]` line markers to coordinate concurrent agents
  - Explicit prohibition of band-aid fixes (no `unknown` casts, no deleting
    "unused" imports without understanding intent)
  - Mandatory adherence to the best-practices and progress-tracker documents

  Added 2025-06-27.
  ([`91e89ea`](https://github.com/Dicklesworthstone/useful_coding_guides_for_llms/commit/91e89eaabdbb12d369678bf5526bb2ec3fe1c3f1))

### Recommended Workflow Section (PR #3)

- Added a "Recommended Workflow" section to `README.md` describing the full
  multi-model research-then-implement pipeline: use Claude Opus, GPT-4o, and
  Gemini Deep Research to compile best practices, merge into a single guide
  with a large-context model, then apply iteratively with Claude Code in
  background-task mode. Includes the `cc` shell alias:
  ```
  alias cc='ENABLE_BACKGROUND_TASKS=1 claude --dangerously-skip-permissions'
  ```

  Added 2025-06-26.
  ([`32e46d4`](https://github.com/Dicklesworthstone/useful_coding_guides_for_llms/commit/32e46d4c23545c99bd0a88ad98e4a594f6292b50),
  merged in [`e04f380`](https://github.com/Dicklesworthstone/useful_coding_guides_for_llms/commit/e04f38027dcc5365772889623abd568e2ef04194))

---

## Documentation and Discoverability

Changes that make the repository easier to find, understand, and consume as a
single-file reference.

### Repository Initialization

- Created the repository with a stub `README.md`.

  2025-06-26.
  ([`b7b7cdd`](https://github.com/Dicklesworthstone/useful_coding_guides_for_llms/commit/b7b7cddd51c069d3c7099a8e6155c82fbc06c643))

### README Overhaul (PR #1)

- Replaced the single-line stub with a proper overview listing every document
  in the repository and its purpose.

  2025-06-26.
  ([`b4b672e`](https://github.com/Dicklesworthstone/useful_coding_guides_for_llms/commit/b4b672ef5c9866e4733f5fa6424e5cb58833f0b3),
  merged in [`e8afe88`](https://github.com/Dicklesworthstone/useful_coding_guides_for_llms/commit/e8afe88f9162cca92cac7512dfc546fecdc73eb1))

### README Expansion with Embedded Guide (PR #2)

- Embedded the entire 960-line best-practices guide directly into `README.md`
  (+997 lines), turning the repository into a single-file reference for
  agentic coding tools. Added sections explaining how to load the README in an
  AI's context window, supply the progress document, and iterate gradually.

  2025-06-26.
  ([`65846fc`](https://github.com/Dicklesworthstone/useful_coding_guides_for_llms/commit/65846fcd7c448e6dda242df0e3f66abe15fbf69f),
  merged in [`b363481`](https://github.com/Dicklesworthstone/useful_coding_guides_for_llms/commit/b3634817fb658260ac8667cd1bdf54cb494209b6))

### Social Preview Image

- Added `gh_og_share_image.png` (1 280 x 640) for consistent Open Graph
  previews when the repository URL is shared on social media.

  2026-02-21.
  ([`c89b1c8`](https://github.com/Dicklesworthstone/useful_coding_guides_for_llms/commit/c89b1c824162904913ad1f88b4a30094d9ddb11b))

---

## Licensing and Governance

- Formalized the project license as **MIT with OpenAI/Anthropic Rider**:
  standard MIT terms for the general public, with an additional restriction
  prohibiting use by OpenAI, Anthropic, and their affiliates without express
  written permission from Jeffrey Emanuel.

  2026-02-21.
  ([`6fe4cd3`](https://github.com/Dicklesworthstone/useful_coding_guides_for_llms/commit/6fe4cd3c58ef8a5dad98c073c85a2f9741124a03))

---

## Tags and Releases

This repository has no git tags and no GitHub releases as of 2026-03-21.

---

## Document Inventory

| File | Lines | Purpose |
|------|------:|---------|
| `README.md` | 1 022 | Full repository overview + embedded best-practices guide |
| `NEXTJS15_BEST_PRACTICES.md` | 960 | Standalone best-practices reference |
| `PROMPT_TO_GENERATE_AND_REVISE_GUIDES.md` | 1 119 | Seed prompts for multi-model research |
| `EXAMPLE_USING_THE_TECHNIQUE_IN_CLAUDE_CODE.md` | 1 948 | Claude Code transcript demonstrating the workflow |
| `NEXT_BEST_PRACTICES_IMPLEMENTATION_PROGRESS.md` | 321 | Per-section implementation checklist |
| `BUG_FIXING_TEMPLATE.md` | 26 | Template for multi-agent bug-fixing sessions |
| `LICENSE` | 73 | MIT with OpenAI/Anthropic Rider |
| `gh_og_share_image.png` | -- | Social preview image (1 280 x 640) |
