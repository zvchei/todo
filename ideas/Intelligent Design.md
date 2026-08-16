# Intelligent Design

Programs evolve to survive in a simulated ecology, however the evolution is not driven by randomness, but an LLM trying to change programs to make them more resilient.

This is one concrete instantiation of the general mechanism described in [Agentic Genetic Programming](Agentic%20Genetic%20Programming.md): the genome is a program, and the agents are the mutators that rewrite it. What sets this idea apart from other instantiations is the *fitness function*: instead of a fixed benchmark or test suite, fitness here is defined by an abstract ecology - a simulated environment in which programs compete for shared, limited resources, reproduce, and die. A program's score is therefore relative to the rest of the population and the current state of the ecology, not to a static target, which should make the resulting solutions more robust to overfitting at the cost of noisier, less directly interpretable selection pressure.

## Next

* Define the ecology's resource model: what do programs compete for (compute cycles, memory, a shared task queue, simulated "food"), and how does scarcity create selection pressure?
* Decide whether programs interact directly (predation, cooperation, parasitism) or only indirectly through resource contention.
* Determine what "surviving" means operationally - a program that stops being scheduled, one that throws unrecoverable errors, or one that is out-competed and removed by the LLM itself?
* Explore whether the LLM mutator should have visibility into the whole ecology (global view) or only into the program it's currently editing plus its immediate outcome (local view), and how that affects convergence.