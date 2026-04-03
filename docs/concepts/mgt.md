---
read_when:
  - designing core architecture
  - naming core nouns
  - designing CLI for agents
  - deciding what "graftable" means
---

# mgt

## One-line

`mgt` is an agent-native CLI for horizontal transfer of small software modules between codebases.

Name layers:

- `mgt` near-term: module graft tool
- `mgt` bigger vision: module gene transfer

Reason:

- "module" names the transferred unit
- "modular" still matters, but as the desired property of the unit and host code
- "graft" fits the initial operational job
- "gene transfer" fits the broader system model and longer-term platform shape

## Why this exists

Current reuse modes weak:

- copy/paste: fast, but brittle
- package manager: heavy, coupled, slow to publish, supply chain risk
- framework generator: opinionated, coarse-grained
- code search: finds code, not transplant-ready modules
- full generation has high outcome variance

Wanted:

- small units
- fast search
- transplant guidance
- machine-readable contracts
- safe apply / verify loop
- intent-aware selection and adaptation
- immediate graft function
- minimized dependency graph
- no donor dependency edge after transfer

On fitness:

We expect contribution of types to future generations (host + specific module + others using `mgt`) relative to other types in the same population and environment.

Dependency stance:

- aggressively minimize runtime/library dependencies introduced by transfer
- do not retain donor package/repo as a dependency edge
- allow practical local support code when needed: shims, bundled helpers, framework glue, fixtures
- optimize for fit in host environment, not ideological zero-dependency purity

## Core inspiration

### Karpathy: bacterial code

Target style:

- small: every line pays rent
- modular: grouped into swappable operons
- self-contained: easy to "yoink"
- forkable: value without full framework/library buy-in

Product implication:

- optimize for graftable modules, not giant abstractions
- prefer explicit local code over deep hidden framework magic
- every exported unit should answer: can an agent lift this cleanly?

### Agent-first CLI

From Justin Poehnelt's agent-first CLI argument:

- raw structured payload path first-class
- schema introspection in-tool
- machine-readable output by default
- strong input validation
- dry-run and safety rails
- skills / MCP / multiple agent surfaces from one core

Implication for `mgt`:

- command API should be stable JSON before pretty text
- every mutating command needs `--dry-run`
- introspection commands should expose schemas, capabilities, adapters
- docs should not be the only source of truth

### Fast local search

From `fff.nvim`:

- native indexing/search
- typo-resistant fuzzy match
- cache + refresh model
- performance as product feature, not polish

Implication for `mgt`:

- index first; grep alone not enough
- hot path likely native
- interactive latency target should feel instant on medium repos
- incremental rescans, cached metadata, git-aware filtering
- built for agents

### Auto-research / evolve

From `autoresearch`:

- some subsystems can improve via automated exploration
- benchmark loops can search config space better than ad hoc tuning

Good candidates inside `mgt`:

- ranking heuristics for graft candidates
- module boundary scoring
- dependency-pruning strategies
- patch adaptation prompts/plans
- benchmark-driven index tuning

Constraint:

- keep core deterministic
- isolate "evolve" paths behind explicit commands / experiments

## Thesis

Software reuse should work more like gene transfer than package publishing.

Best reusable unit often not:

- whole repo
- framework
- library release

Best reusable unit often is:

- one function
- one helper module
- one CLI command
- one test harness
- one adapter

If `mgt` works, an agent should be able to:

1. locate a useful module fast
2. align with user intent for this change
3. understand dependencies and contract
4. adapt it to host repo
5. graft it in with minimal collateral change
6. verify fitness
7. later: critique donor/host fit with deeper intent models

## Core nouns

- host: target repo receiving code
- donor: source repo or source module
- gene: smallest useful transferable unit; usually function or tiny type/helper
- operon: small cohesive set of genes transferred together
- plasmid: portable bundle with metadata, tests, examples, dependencies
- graft: transplant operation into host
- compatibility layer: shims/adapters needed for host fit
- fitness: post-graft quality score from tests/lints/runtime checks
- donor intent: what the source module/repo is trying to achieve
- host intent: what the target repo is trying to achieve
- user intent: what the operator wants changed now
- lineage: pinned provenance of where a transferred module came from

## Product shape

### Job to be done

"Take this capability from there; make it work here; show me risk."

Real hidden job:

"Figure out what that code means there, what this repo needs here, and what I actually want now."

### Likely users

- coding agents
- staff/principal engineers moving patterns across repos
- OSS maintainers extracting gist-quality modules

### Non-goals

- not another package manager
- not full semantic refactor platform on day one
- not autonomous repo rewriting without operator control
- not limited to one language long term, but likely start narrow
- not focused on search 

## CLI shape v0

Principles:

- human-readable + JSON, but JSON primary
- composable subcommands
- explicit scopes
- dry-run default for writes early on
- donor-known transfer first; discovery later

Sketch:

```text
mgt add <repo> --ref <sha> --path <module> --to <dst> --mode copy|adapt|synthesize [--json]
mgt verify <dst> [--json]
mgt refresh <dst> [--json]
mgt diff <dst> [--json]
mgt schema <command>
mgt mcp
```

## Core capabilities

### 1. Resolve intent

This looks like the hard problem, not side metadata.

Three intents to model:

- donor intent: what current/source repo or module is for
- host intent: what target repo is for
- user intent: what change is wanted right now

Priority order:

- v0: user intent primary; assume donor selection mostly correct
- next: donor/host assessment and warning
- later: donor discovery/search feeding upstream into this chain

Why this matters:

- same module can be valid in one host and wrong in another
- best donor code may be technically similar but semantically wrong
- adaptation quality depends on knowing invariants, not just syntax

Signals for donor/host intent:

- README/docs
- package metadata
- CLI verbs / API surfaces
- tests
- directory names
- commit history / PR context later
- surrounding call sites

Signals for user intent:

- natural language prompt
- current task context
- selected files/symbols
- explicit constraints: speed, no deps, framework, style, safety

Output:

- intent summary
- confidence
- unresolved ambiguities
- constraints for search/ranking/adaptation
- warnings when donor/host intent appear misaligned later in roadmap

### 2. Detect graftable modules

Need:

- symbol extraction
- boundary detection
- import/dependency graph
- local test/example detection
- side-effect scoring
- donor dependency graph walk for required local transfer set

Outputs:

- transfer unit
- dependencies
- required env/runtime assumptions
- confidence / portability score

### 3. Rank candidates

Signals:

- lexical match
- symbol/API similarity
- dependency cost
- self-containment
- test presence
- host compatibility
- freshness / quality signals
- donor/host/user intent alignment

Note:

- open-ended discovery/search is not the first wedge
- ranking matters later once donor choice becomes less operator-specified

### 4. Adapt for host

Adaptation tasks:

- rename imports
- inline tiny deps or bundle them
- generate shims
- translate framework glue
- rewrite config paths
- propose follow-up TODOs
- preserve host intent, not donor quirks by default
- prune unnecessary dependencies while preserving behavior

### 5. Verify fitness

Checks:

- compile / typecheck
- targeted tests
- lint
- dead import / unused code scan
- optional benchmark/regression
- intent-fit checks where possible

## Data model sketch

### Module manifest

Possible fields:

```json
{
  "id": "module:hash",
  "kind": "function",
  "language": "ts",
  "name": "parseFrontmatter",
  "path": "src/frontmatter.ts",
  "source": {
    "repo": "github.com/org/repo",
    "ref": "abc123",
    "path": "packages/foo/src/bar"
  },
  "exports": ["parseFrontmatter"],
  "imports": ["node:fs"],
  "internal_deps": ["module:abc"],
  "tests": ["tests/frontmatter.test.ts"],
  "side_effects": "low",
  "portability_score": 0.88,
  "donor_intent": {
    "summary": "Parse simple frontmatter from local markdown content",
    "confidence": 0.79
  }
}
```

### Graft plan

Possible fields:

```json
{
  "donor_modules": ["module:hash"],
  "host_path": "src/lib/frontmatter.ts",
  "intent": {
    "donor": "portable markdown parsing helper",
    "host": "content ingestion pipeline",
    "user": "add frontmatter parsing without new dependency"
  },
  "actions": [
    "copy module",
    "inline helper trimBom",
    "rewrite import path",
    "add targeted tests"
  ],
  "risks": [
    "depends on node filesystem APIs",
    "host uses different error envelope"
  ]
}
```

## Architecture sketch

### Layers

- CLI shell
- command schema/introspection
- intent inference
- provenance / lineage store
- indexer
- graph/build of module manifests
- ranking engine
- graft planner
- adapter engine
- verifier

### Performance stance

- local-first
- cache-heavy
- incremental refresh
- native or hybrid native for index/ranking hot paths
- streaming output for agent pipelines

### Safety stance

- dry-run
- explicit write boundaries
- path validation
- deterministic patch preview
- provenance: record donor source + transforms

## Language strategy

Best first wedge:

- TypeScript/JavaScript

Reason:

- high agent usage
- frequent copy/paste reuse
- lots of small utilities
- AST/tooling mature

Good second wave:

- Python
- Go
- Rust

## Repository strategy

Two plausible tracks:

### Track A: standalone binary

- Rust core
- speed, static binary, native indexing

### Track B: TS prototype then native core

- faster iteration on heuristics
- later move index/ranking hot path native

Current lean:

- prototype fast in TypeScript if discovery mode
- move hot path native once data proves bottleneck

Counterpoint:

- if "fast" is core brand promise from day one, Rust early may be cleaner

## Auto-research lane

Keep this separate from default UX.

Possible commands:

```text
mgt evolve ranker
mgt evolve intent-inference
mgt evolve adapter --task react-hook-to-solid
mgt benchmark search
```

Rules:

- no silent self-modification
- experiment outputs explicit
- benchmark dataset versioned
- promoted heuristics only after human review

## Execution docs

- [V0](/Users/rgbrgy/Code/github/ryangriffinau/mgt/docs/V0.md)
- [Roadmap](/Users/rgbrgy/Code/github/ryangriffinau/mgt/docs/ROADMAP.md)

## Sharp questions

- Is unit of transfer source code only, or tests/docs/config too?
- Should `mgt` prefer copying code over creating dependencies almost always?
- When host and donor frameworks differ, when do we stop adapting and say "not graftable"?
- Is best UX repo-local, global cache, or both?
- Should module manifests be ephemeral or checked into repo?
- Is "graftability" a static property or host-relative score?
- how much intent inference should be automatic vs explicit user-supplied constraints?
- should `mgt` fail closed when donor intent and user intent diverge?

## Working position

Biases worth testing early:

- package-install path is fallback, not default
- transferable unit should include tests/examples when possible
- agent-native contract matters as much as code quality
- performance budget should be strict enough that repeated use feels cheap
- graft ranking should optimize for intent fit before superficial code similarity
- external repos/modules first; self-host after proof

## References

- Andrej Karpathy, bacterial code / horizontal transfer framing
- Justin Poehnelt, "You Need to Rewrite Your CLI for AI Agents": [justin.poehnelt.com/posts/rewrite-your-cli-for-ai-agents/](https://justin.poehnelt.com/posts/rewrite-your-cli-for-ai-agents/)
- `fff.nvim`: [github.com/dmtrKovalenko/fff.nvim](https://github.com/dmtrKovalenko/fff.nvim)
- `autoresearch`: [github.com/karpathy/autoresearch](https://github.com/karpathy/autoresearch)
