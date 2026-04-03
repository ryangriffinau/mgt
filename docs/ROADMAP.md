---
read_when:
  - planning phases
  - deciding priority
  - adding new capabilities
---

# Roadmap

Priority order. Earlier items protect correctness of later ones.

## Phase 0: framing

- lock nouns
- lock CLI philosophy
- lock dependency policy
- define v0 success criteria
- keep provenance first-class

## Phase 1: v0 donor-known transfer

See [V0](/Users/rgbrgy/Code/github/ryangriffinau/mgt/docs/V0.md).

- pinned donor tuple: `repo + ref + path`
- modes: `copy`, `adapt`, `synthesize`
- transfer required local donor set
- validate in host
- emit lineage / lock metadata
- prove on external repos/modules

## Phase 2: donor graph + refresh quality

- improve donor dependency graph walking
- better hidden dependency detection
- tighter refresh / diff model
- fixture/equivalence testing against donor behavior
- bounded repair loop from verifier feedback

## Phase 3: donor monitoring

- monitor donor updates affecting transferred modules
- show impact on local grafts
- support refresh against newer pinned refs
- preserve human review point before adoption

## Phase 4: intent expansion

- deepen donor intent understanding
- infer host repo intent
- warn on donor/host/user intent mismatch
- feed intent into ranking, adaptation, verification

## Phase 5: discovery

- donor/module discovery
- search across candidate donors
- ranking across portability, intent fit, dependency cost
- reuse upstream search/index tooling where practical

## Phase 6: self-hosting

- use `mgt` on `mgt` internal modules
- use `mgt` to prune `mgt`'s own dependency graph
- keep external corpus as control benchmark
- measure whether self-hosting improves actual dependency, portability, and fitness outcomes

## Phase 7: evolve lane

- benchmark harness
- ranking experiments
- intent-inference experiments
- adapter experiments
- explicit promotion path from experiment to stable behavior
