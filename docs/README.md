---
read_when:
  - starting work in this repo
  - changing product shape, CLI surface, architecture, or terminology
---

# Docs

Entry point. Single source of truth for `mgt` direction until code exists.

## What is `mgt`

`mgt` = module graft tool.

Extended reading:

- near-term name: module graft tool
- broader / ultimate form: module gene transfer
- key property: modules should be modular enough to transfer cleanly

- goal: fast agent-native CLI for finding, extracting, adapting, and grafting small useful code modules
- mental model: bacterial horizontal gene transfer
- bias: small modules, low coupling, easy lift-out, easy transplant, fast validation

## Read Order

1. [Concept: mgt](/Users/rgbrgy/Code/github/ryangriffinau/mgt/docs/concepts/mgt.md)
2. [V0](/Users/rgbrgy/Code/github/ryangriffinau/mgt/docs/V0.md)
3. [Roadmap](/Users/rgbrgy/Code/github/ryangriffinau/mgt/docs/ROADMAP.md)
4. [Changelog](/Users/rgbrgy/Code/github/ryangriffinau/mgt/docs/CHANGELOG.md)

## Current Decisions

- product starts as CLI, not GUI
- primary user: coding agents; human operator secondary
- machine-readable I/O first-class
- fast local indexing/search critical path
- module boundaries matter more than framework cleverness
- v0 assumes operator chose donor correctly
- user/agent intent matters most first; donor/host intent expands later
- prefer local transferred code over donor/runtime dependency edges
- self-pruning dogfood matters, but only after external proof

## Open Questions

- exact substrate: files, symbols, snippets, tests, configs, prompts?
- graft unit: function, class, module, command, workflow, doc chunk?
- safety model: suggest-only vs auto-apply vs patch queue?
- adaptation depth: literal copy, shim generation, dependency hoist, API rewrite?
- persistence: local cache only vs shared registry/network later?
- how should intent inference work, and when should user override it?
- when do we permit new helper/toolchain deps vs reject them?
