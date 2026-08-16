# Agentic Genetic Programming

> A generalisation of the mechanism explored in [Intelligent Design](Intelligent%20Design.md), [Evolutionary Prompt Optimisation](Evolutionary%20Prompt%20Optimisation.md), and the "Evolutionary Skill Adaptation" section of [Self-modifying agents](Self-modifying%20agents.md). Also inspired by Karpathy's [autoresearch](https://github.com/karpathy/autoresearch) and a growing body of external work (see Prior Art below).

## Concept

Classic genetic programming evolves a population of candidate solutions through blind, syntactic operators - random mutation, crossover of opaque token/bit sequences - guided only by a fitness function. This idea replaces the "genetic" operators with AI agents: an LLM reads a candidate, understands *why* it scores the way it does, and proposes a targeted, semantically-informed variant instead of a random one. The fitness function, the population/selection loop, and the notion of a "genome" all stay abstract and pluggable - only the mechanism generating variation changes.

The framework has three abstract roles, deliberately decoupled from any specific domain:

* **Genome** - the artifact being evolved. It can be literally anything an agent can read and rewrite: source code, a system prompt, an agent's own skill/tool definitions, a configuration graph, a simulated organism's ruleset, an agent architecture/scaffold. The genome is opaque to the fitness function - only its behaviour when evaluated matters.

* **Fitness function / Ecology** - whatever produces a score (or ranking) for a genome. This is the part that defines the *task*: it can be a unit-test suite, a benchmark, a simulated environment the genome must survive in, a virtual world a specimen agent must navigate, or a composite of several signals. Crucially, the fitness function need not be a static evaluator - it can itself be a dynamic ecology (see [Intelligent Design](Intelligent%20Design.md)) where genomes compete against each other rather than against a fixed target.

* **Agents** - one or more LLM-driven roles that replace the classic genetic operators:
  * *Mutator* - reads a genome plus its evaluation/error context and proposes a targeted variant (not a random perturbation).
  * *Crossover agent* - semantically merges traits from two or more high-scoring genomes, understanding intent rather than splicing bit strings.
  * *Evaluator* - runs or scores a genome against the fitness function (may itself be partially LLM-driven for fuzzy metrics).
  * *Selector / Researcher* - orchestrates the loop: decides who breeds, who's pruned, when to stop, and can prune obviously non-viable candidates *before* they consume evaluation budget - something blind evolutionary search cannot do.

## Why this generalises the existing ideas

| Instantiation | Genome | Fitness function | Notes |
|---|---|---|---|
| [Intelligent Design](Intelligent%20Design.md) | A program | Survival in a simulated ecology | Fitness is dynamic/relative (competing programs), not a fixed score |
| [Evolutionary Prompt Optimisation](Evolutionary%20Prompt%20Optimisation.md) | A system prompt | Task performance in a virtual environment | "Memes" = decomposable prompt fragments; researcher agent prunes/crosses at meme boundaries |
| [Self-modifying agents](Self-modifying%20agents.md) (Evolutionary Skill Adaptation) | An agent skill/tool | Pass rate on a test-case corpus | Population thinned by fixed top-N, not percentage |
| [AlphaEvolve](https://en.wikipedia.org/wiki/AlphaEvolve) / [OpenEvolve](https://github.com/codelion/openevolve) | Arbitrary source code | User-supplied evaluation function | General-purpose; found new math constructions, GPU kernels, datacenter heuristics |
| [FunSearch](https://en.wikipedia.org/wiki/FunSearch) | A program (from a skeleton) | Programmatic scorer | Island-based population database to preserve diversity |
| [Darwin Gödel Machine](https://sakana.ai/dgm/) | The coding agent's own codebase | Its own performance on coding benchmarks | Self-referential: the agent evolves itself, not an external artifact |
| Karpathy's [autoresearch](https://github.com/karpathy/autoresearch) | Training code (`train.py`) | Validation bits-per-byte, fixed time budget | Orchestration logic itself lives in an editable `program.md` |

The pattern that emerges: **(genome, fitness function, agent roles)** is the whole design space. Every project above is a specific choice along these three axes, and most of the genuinely open questions are about how those axes interact.

## Open Questions

* **Abstraction boundary** - Where should genome and fitness function be decoupled cleanly enough that the same orchestration loop (evaluate → select → mutate/crossover → repeat) works unmodified across domains as different as code, prompts, and simulated ecologies?
* **Self-reference vs. external evolution** - Darwin Gödel Machine evolves the agent itself; AlphaEvolve/autoresearch evolve an external artifact. Does a self-referential loop need different safeguards (e.g. against runaway or degenerate self-modification) than an external one?
* **Dynamic vs. static fitness** - A fixed benchmark score is easy to optimise against and easy to overfit. A relative/ecological fitness (compete against siblings, as in Intelligent Design) resists overfitting but is noisier and harder to reason about. Is there a general recipe for choosing between them, or a way to combine both (benchmark as baseline, ecology as tie-breaker)?
* **Pruning before evaluation** - The agent-driven advantage over blind GP is the ability to discard obviously non-viable candidates before spending evaluation budget. How much of the total compute saved comes from this versus from smarter (non-random) mutation itself?
* **Cost model** - Every generation requires multiple LLM calls per genome (mutate, evaluate if LLM-scored, crossover). What's the right population size and generation count for a given compute budget, and can cheaper/smaller models serve as a first-pass filter before a stronger model does final scoring?
* **Diversity collapse** - Shared across all instantiations: how to stop the population converging on one strategy and losing the ability to escape local optima (island models, novelty search, explicit niching)?
* **Generalised orchestrator prompt** - Is there a single "researcher/selector" agent design that works across genome types, or does each domain need its own orchestration logic (as autoresearch's editable `program.md` suggests)?

## Related Ideas

* [Intelligent Design](Intelligent%20Design.md) - the most concrete instantiation in this collection: programs evolving in a simulated ecology, with an LLM (not randomness) driving mutation.
* [Evolutionary Prompt Optimisation](Evolutionary%20Prompt%20Optimisation.md) - the prompt-as-genome instantiation, with detailed treatment of meme decomposition, ablation, and crossover.
* [Self-modifying agents](Self-modifying%20agents.md) - the skill-as-genome instantiation, plus a single-agent (non-population) analogue of the same feedback loop.
* [Autopoietic Workflow Orchestrator](Autopoietic%20Workflow%20Orchestrator.md) - a closed-loop, non-population variant: errors drive re-synthesis of a single workflow node rather than selection across a population.
