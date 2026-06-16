# Evolutionary Prompt Optimisation

> Partially inspired by Karpathy's [autoresearch](https://github.com/karpathy/autoresearch) concept, but also building on prior ideas in this collection around virtual environments for AI agents and self-modifying agent architectures.

## Concept

A framework for automatically discovering effective prompts through evolutionary optimisation. The system has three core components: a virtual environment, a population of specimen agents, and a researcher agent.

### Virtual Environment

A sandboxed environment exposed to an LLM agent exclusively through a set of tool calls. The environment maintains internal state, accepts actions, and returns observations. It can be anything from a text-based game world to an abstract task simulator - the only requirement is that it can evaluate agent performance and produce a numerical score.

### Specimen Agents

Each specimen is an LLM agent instantiated with a distinct system prompt (constructed by the researcher). Multiple specimens run in parallel against the virtual environment, each given a fixed budget of time or iterations to solve a task. A specimen interacts with the environment through tool calls and has no knowledge of the optimisation process surrounding it or of the other specimens. Each specimen is a point in prompt-space; the population as a whole explores that space, reducing the risk of getting trapped in local minima.

### Researcher Agent

An outer-loop LLM agent that orchestrates the entire optimisation cycle:

1. **Initialisation** - Generate an initial population of system prompts, one per specimen.
2. **Evaluation** - Run all specimens in parallel against the virtual environment. Record each specimen's score.
3. **Selection** - Rank specimens by score and select the top performers.
4. **Analysis** - Decompose high-scoring prompts into semi-independent units ("memes"): distinct instructions, constraints, heuristics, or stylistic choices that can be individually toggled. Run ablation passes - disable individual memes and re-evaluate - to score each meme on two axes: **impact** (how much it contributes to the total score) and **sensitivity** (how much the score changes when the meme is rephrased or perturbed). High-impact, low-sensitivity memes are robust core genes; high-impact, high-sensitivity memes are fragile and need careful handling during crossover.
5. **Crossover** - Combine high-scoring prompts at meme boundaries, informed by the analysis phase. Tag each meme with its parent origin so that post-crossover evaluation can attribute score changes to specific inherited components rather than treating the offspring as an opaque whole.
6. **Mutation** - Apply random perturbations: rephrase instructions, add or remove constraints, change emphasis, inject new heuristics. Mutation intensity can be guided by sensitivity scores - robust memes tolerate aggressive mutation, fragile ones get gentler treatment.
7. **Iteration** - Spawn a new generation of specimens from the evolved prompts and repeat.

The key insight is that the researcher itself is an LLM, which means the crossover and mutation steps are semantic rather than syntactic. The LLM can understand the intent behind a prompt fragment and recombine ideas meaningfully, unlike traditional genetic algorithms that operate on opaque bit strings. Crucially, the LLM researcher can use its own understanding to prune non-viable branches from the tree of probable solutions before they are ever evaluated - discarding prompt variants that are incoherent, contradictory, or obviously unfit. This guided pruning should dramatically reduce the search space compared to blind evolutionary strategies, where non-viable offspring consume evaluation budget before being eliminated by low scores. The population-based approach - running multiple specimens per generation - provides the diversity needed to escape local minima and explore qualitatively different strategies in parallel.

### Scoring

The score function is defined by the virtual environment and the task. It could be a simple pass/fail, a count of objectives completed, time-to-completion, or a composite metric. The researcher uses scores to drive selection pressure across the population.

## Open Questions

* **Population size vs. evaluation cost** - Each specimen requires multiple LLM calls. How large can the population be before cost becomes prohibitive? Can cheaper models serve as specimen proxies for initial filtering?
* **Prompt diversity** - How to prevent the population from collapsing into a single strategy? Niching, novelty search, or explicit diversity metrics may help.
* **Transferability** - Do prompts optimised for one environment generalise to similar environments, or are they brittle? How to encourage the evolution of robust, generalisable prompts rather than overfitting to specific quirks of the environment?
* **Researcher prompt** - The researcher itself needs a prompt. Is there a meta-optimisation problem here, or does a generic "evolve better prompts" instruction suffice? Can we evolve a better researcher?
* **Stochasticity** - LLM outputs are non-deterministic. Each specimen may need multiple evaluations to get a reliable score estimate, further increasing cost. Is relaying on a single seed per specimen sufficient?
* **Convergence criteria** - When to stop iterating: score plateau, generation count, or computational budget?

## Related Ideas

Several existing articles in this collection touch on components or themes that directly feed into this concept:

* [Text-based game for AI agents](Text-based%20game%20for%20AI%20agents.md) - Describes a virtual environment where LLM agents interact through structured tool calls, select actions from context-dependent lists, and solve hierarchical goals. This is a natural candidate for the virtual environment component.

* [Non-player characters](Non-player%20characters.md) - Explores agents in virtual environments with behavioural goals, cognitive frameworks, adaptive learning, and iterative development. The emphasis on measurable agent performance and iterative refinement connects to the evaluation loop.

* [Self-modifying agent](Self-modifying%20agent) - An agent that iteratively updates its own instructions to improve performance. This is the single-agent analogue of what the researcher does across a population of specimens.

* [Autopoietic Workflow Orchestrator](Autopoietic%20Workflow%20Orchestrator) - A system that treats errors as feedback and continuously refines its own logic through closed-loop evolution. The "autopoietic resilience" principle mirrors the evolutionary loop here.