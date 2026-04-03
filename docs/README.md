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
- broader / ultimate form: module gene transfer (no dependencies, fit for purpose)

- goal: fast agent-native CLI for finding, extracting, adapting, and grafting small useful code modules
- mental model: bacterial horizontal gene transfer
- bias: small modules, low coupling, easy lift-out, easy transplant, fast validation

## Read Order

1. [Concept: mgt](/Users/rgbrgy/Code/github/ryangriffinau/mgt/docs/concepts/mgt.md)
2. [Changelog](/Users/rgbrgy/Code/github/ryangriffinau/mgt/docs/CHANGELOG.md)

## Current Decisions

- product starts as CLI, not GUI
- primary user: coding agents; human operator secondary
- machine-readable I/O first-class
- fast local indexing/search critical path
- module boundaries matter more than framework cleverness
- success depends on intent resolution across source, target, and operator

## Open Questions

- exact substrate: files, symbols, snippets, tests, configs, prompts?
- graft unit: function, class, module, command, workflow, doc chunk?
- safety model: suggest-only vs auto-apply vs patch queue?
- adaptation depth: literal copy, shim generation, dependency hoist, API rewrite?
- persistence: local cache only vs shared registry/network later?
- how should intent inference work, and when should user override it?
