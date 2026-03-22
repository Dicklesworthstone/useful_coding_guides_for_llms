# Changelog

All notable changes to **useful_coding_guides_for_llms** are documented here.

This repository has no formal release tags. The changelog is organized
chronologically by commit, grouped into logical phases of the project's
evolution. Every link points to the live commit on GitHub.

Repository: <https://github.com/Dicklesworthstone/useful_coding_guides_for_llms>

---

## 2026-02-21 — Licensing and Social Preview

Two housekeeping commits that added branding assets and formalized the
project's license.

### License

- Replaced the default MIT license with **MIT + OpenAI/Anthropic Rider**,
  restricting use by OpenAI, Anthropic, and their affiliates without express
  written permission from Jeffrey Emanuel.
  ([`6fe4cd3`](https://github.com/Dicklesworthstone/useful_coding_guides_for_llms/commit/6fe4cd3c58ef8a5dad98c073c85a2f9741124a03))

### Social Preview

- Added `gh_og_share_image.png` (1280x640) for consistent Open Graph link
  previews when the repository URL is shared on social media.
  ([`c89b1c8`](https://github.com/Dicklesworthstone/useful_coding_guides_for_llms/commit/c89b1c824162904913ad1f88b4a30094d9ddb11b))

---

## 2025-06-27 — Bug Fixing Template

- Added `BUG_FIXING_TEMPLATE.md`: a template prompt for directing agentic
  coding assistants (e.g., Claude Code) to systematically fix type-check and
  linter errors from a combined output file. Includes guidance on random
  chunking for multi-agent parallelism, marking completed items, and adhering
  to the best-practices guide during fixes.
  ([`91e89ea`](https://github.com/Dicklesworthstone/useful_coding_guides_for_llms/commit/91e89eaabdbb12d369678bf5526bb2ec3fe1c3f1))

---

## 2025-06-26 — Initial Repository Build-out

The entire repository was created in a single evening through a rapid sequence
of commits and three Codex-authored pull requests. The commits below are listed
in chronological order.

### Repository Initialization

- Created the repository with a stub `README.md`.
  ([`b7b7cdd`](https://github.com/Dicklesworthstone/useful_coding_guides_for_llms/commit/b7b7cddd51c069d3c7099a8e6155c82fbc06c643))

### Core Guides Uploaded

- **`NEXTJS15_BEST_PRACTICES.md`** (961 lines) — the primary reference
  document. A comprehensive, production-grade architectural guide for Next.js
  15, the App Router, and Zustand covering:
  1. Foundational architecture and file organization (scalable `src` layout,
     colocated route logic, Partial Pre-rendering)
  2. Server-first data paradigm (Server Components, caching changes in
     Next.js 15, React 19 integration)
  3. Data mutations via Server Actions (form handling, `useMutation`,
     optimistic UI, security enhancements)
  4. Zustand for client-side UI state (per-request stores, `StoreInitializer`
     pattern, `useShallow`, advanced patterns)
  5. Secure authentication with `httpOnly` cookies and Server Actions
     (including CVE-2025-29927 notes)
  6. End-to-end type safety with OpenAPI client generation
  7. Addendum: advanced caching (`unstable_cache`), `after()` API, client
     router cache controls, observability, Edge Runtime, performance
     monitoring, React Compiler, full-stack type safety, scalable component
     architecture

- **`NEXT_BEST_PRACTICES_IMPLEMENTATION_PROGRESS.md`** (322 lines) — a
  checklist tracking adoption of every section of the best-practices guide in
  a real codebase, with percentage-complete annotations and priority actions.

  ([`230cef5`](https://github.com/Dicklesworthstone/useful_coding_guides_for_llms/commit/230cef55ec269c96642ee30125a4050005a3a10f))

### Prompt Engineering Guide

- **`PROMPT_TO_GENERATE_AND_REVISE_GUIDES.md`** (1119 lines) — the seed prompt
  used to direct multiple LLMs (Claude Opus, GPT-4o, Gemini Deep Research) to
  research current best practices, then merge and iteratively refine the
  results into a single authoritative guide.
  ([`44fee35`](https://github.com/Dicklesworthstone/useful_coding_guides_for_llms/commit/44fee3535d6082ad2dd267442a0d57c21363288f))

### README Revision (PR #1)

- Expanded the stub README into a proper overview listing the repository's
  documents and their purposes.
  ([`b4b672e`](https://github.com/Dicklesworthstone/useful_coding_guides_for_llms/commit/b4b672ef5c9866e4733f5fa6424e5cb58833f0b3),
  merged in [`e8afe88`](https://github.com/Dicklesworthstone/useful_coding_guides_for_llms/commit/e8afe88f9162cca92cac7512dfc546fecdc73eb1))

### Claude Code Transcript

- **`EXAMPLE_USING_THE_TECHNIQUE_IN_CLAUDE_CODE.md`** (1948 lines) — a full
  transcript demonstrating how to iteratively apply the best-practices guide
  with Claude Code. Shows the step-by-step workflow of reading the guide,
  updating the progress tracker, and implementing patterns like `unstable_cache`
  integration across a real codebase.
  ([`06c6cd9`](https://github.com/Dicklesworthstone/useful_coding_guides_for_llms/commit/06c6cd94385bcd6c9f2e3e8aa627603611ae6993))

### README Expansion with Full Guide (PR #2)

- Embedded the entire best-practices guide directly into `README.md` (+997
  lines), making the repository a single-file reference for agentic coding
  tools. Added sections on using the repo with AI assistants and the
  recommended workflow.
  ([`65846fc`](https://github.com/Dicklesworthstone/useful_coding_guides_for_llms/commit/65846fcd7c448e6dda242df0e3f66abe15fbf69f),
  merged in [`b363481`](https://github.com/Dicklesworthstone/useful_coding_guides_for_llms/commit/b3634817fb658260ac8667cd1bdf54cb494209b6))

### Workflow Guidance (PR #3)

- Added a "Recommended Workflow" section to `README.md` describing the
  multi-model research process: using Claude Opus, GPT-4o, and Gemini Deep
  Research to compile best practices, then applying them iteratively with
  Claude Code in background-task mode. Includes the `cc` shell alias for
  launching Claude Code with background tasks enabled.
  ([`32e46d4`](https://github.com/Dicklesworthstone/useful_coding_guides_for_llms/commit/32e46d4c23545c99bd0a88ad98e4a594f6292b50),
  merged in [`e04f380`](https://github.com/Dicklesworthstone/useful_coding_guides_for_llms/commit/e04f38027dcc5365772889623abd568e2ef04194))

---

## Document Inventory

| File | Lines | Purpose |
|------|------:|---------|
| `README.md` | 1022 | Full repository overview + embedded best-practices guide |
| `NEXTJS15_BEST_PRACTICES.md` | 960 | Standalone best-practices reference |
| `PROMPT_TO_GENERATE_AND_REVISE_GUIDES.md` | 1119 | Seed prompts for multi-model research |
| `EXAMPLE_USING_THE_TECHNIQUE_IN_CLAUDE_CODE.md` | 1948 | Claude Code transcript demonstrating the workflow |
| `NEXT_BEST_PRACTICES_IMPLEMENTATION_PROGRESS.md` | 321 | Per-section implementation checklist |
| `BUG_FIXING_TEMPLATE.md` | 26 | Template for multi-agent bug-fixing sessions |
| `LICENSE` | 74 | MIT with OpenAI/Anthropic Rider |
| `gh_og_share_image.png` | — | Social preview image (1280x640) |
