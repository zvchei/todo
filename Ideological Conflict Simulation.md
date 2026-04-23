# Simulating Ideological Conflict via Semantic Mapping — DRAFT 2026-04-23

**Authors:** zvchei & Claude Sonnet 4.6
**License:** [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/) — public domain

## Core Hypothesis

A belief system can be modeled as a weighted directed graph of ideas and memories, where an **ideology** emerges as a cluster of mutually reinforcing nodes — not by design, but as a consequence of repeated traversal and reinforcement. Conflicting information is not eliminated; it is **suppressed**: pushed below the threshold of conscious thought through battles that reduce node weight, and gradually starved of traversal through dreaming. But suppressed nodes are never deleted. They persist as **latent dissenters** — invisible to thinking, occasionally surfaced by dreaming, slowly accumulating weight each time an incoming experience brushes against them.

This structural permanence of suppressed conflict may be what underlies PTSD-adjacent symptoms in radicalized individuals: not guilt in a conscious sense, but a graph that cannot fully forget what it has been forced to stop thinking about. The suppression cost is readable directly from the graph — and it scales with the degree of distortion required to maintain the dominant ideology.

---

## Possible Formalization

### Graph Structure

Model the belief system as a **weighted directed graph**, where:

- **Nodes** represent individual ideas, thoughts, or memories. Each node carries:
  - a **type**: `idea` or `memory`
  - a **node weight** — determines how likely the node is to be selected as a seed when initiating dreaming or thinking. This is the primary suppression mechanism: a node with sufficiently low weight relative to others becomes effectively invisible to conscious processes
  - a **timestamp** — `memory` nodes only. Records when the memory was acquired, used exclusively during learning to identify the most recent memory and establish causal sequencing. Ideas have no timestamp; they exist outside the temporal chain
  - a **content** representation (semantic vector or symbolic label)
  - a **stability** coefficient — memories are more stable than ideas by default

- **Edges** represent *relations* between nodes — predominantly positive, meaning the source node is part of the reasoning basis for the target. An edge is semantically a **"therefore"**: *given this idea or memory, this other one follows*. Edges carry:
  - an **edge weight** — determines how easily traversal flows from one node to another during dreaming or thinking. High edge weight means the connection is readily followed; low edge weight means it is rarely crossed even when the source node is active
  - **sign** (positive = supports/follows from; negative = contradicts)
  - **origin type**: `derived` (idea built on other ideas) or `consequential` (memory following from prior memory)

Node weight and edge weight are **distinct suppression axes**: a node can be heavily suppressed (low node weight, rarely seeded) yet still reachable via high-weight edges from an active neighbor — and vice versa. Full suppression requires both.

- **Ideology** is defined as a cluster of tightly interconnected nodes — a subgraph with high internal edge density and coherent mutual support. It is not a declared structure but an **emergent attractor**: nodes that strongly imply each other become functionally inseparable. The cluster resists outside activation not by design but by topology.

---

### Processes

#### 1. Dreaming
Primarily a **forgetting mechanism** — a passive, consolidating process that operates on existing nodes without generating anything new.

- A random node is selected as a seed, weighted by **node weight** (higher weight = more likely to be seeded). Dreaming is **lax** about this: even low-weight nodes have a nonzero chance of being seeded, unlike thinking
- Its connections are traversed stochastically, following **edge weights** — high-weight edges are crossed readily, low-weight edges rarely
- This produces a small **activated subgraph** of a few nodes
- The nodes in this subgraph are evaluated for **mutual compatibility**
- Based on compatibility:
  - Compatible nodes: edge weights between them are strengthened; node weights may increase slightly; content may subtly drift closer together
  - Incompatible nodes: edge weights are reduced; node weights decay; memories resist weight changes much more strongly than ideas
- Nodes whose node weight falls below an absolute minimum, and whose incoming edge weights have all decayed similarly, become **isolated** — the operational definition of *forgetting*

Dreaming does not resolve conflicts — it only shifts weights. A suppressed node remains in the graph and can still be seeded or reached by traversal, just with diminishing probability.

#### 2. Thinking
An active, **generative** process that creates new ideas — but operates under strict constraints: it never modifies memories, and it changes existing ideas only through conflict resolution, not direct rewriting.

- Two nodes are selected as inputs, sampled by **node weight** — but thinking enforces a **relative weight threshold**: nodes below it are never selected. Suppressed ideas are invisible to thinking; they cannot be consciously reasoned from
- The system attempts to synthesize a **new `idea` node** that is compatible with both, deriving a summary or conclusion
- If the inputs are compatible: the new node is created with `derived` edges back to both sources; initial node weight is moderate
- If the inputs are **in conflict**: a *battle* is triggered — a self-reflection process in which the two competing ideas are evaluated against the broader graph for relative support
  - The idea with stronger overall graph support *wins*; the losing idea has its **node weight suppressed**, potentially below the thinking threshold — making it invisible to future thinking passes
  - **Crucially: the losing idea is not deleted.** It persists with reduced node weight, its edges intact, and remains reachable during dreaming
  - No new synthesis node is created from a battle; the outcome is suppression, not resolution

This asymmetry is important: thinking can surface contradictions explicitly (the battle), but it cannot eliminate them. The losing idea becomes a **latent dissenter** — below the threshold of conscious thought, yet still present in the graph and still traversable during dreaming.

#### 3. Learning
The influx of external events, stored as memories.

- A new `memory` node arrives from the environment, stamped with the current time
- The system requires it to have **at least one causal connection** — it must follow from something. The primary candidate is the most recent memory
- If a plausible logical link to the most recent memory exists: the new memory is connected with a `consequential` edge of moderate strength, extending the main chain
- If no plausible link exists: the system **generates a bridging node** — a confabulated memory — inserted between the most recent memory and the new arrival, stitching the chain back together
- Once anchored to the main chain, the new memory also scans **other recent memories** for compatibility — not just the immediate predecessor. Compatible recent memories form **side-chain connections**: weaker `consequential` edges that reflect thematic or contextual similarity rather than strict temporal sequence. This means the memory graph is not a pure chain but a braided structure: a spine of strict temporal succession with lateral connections between memories that share context, emotion, or subject matter
- All new memories are weakly connected by default; edge and node weights accumulate through dreaming and repeated activation

Timestamp serves one purpose: establishing recency order, so the system knows what the new arrival must causally follow from. It plays no role in dreaming or thinking.

---

### Ideological Suppression in This Model

"Ideological resolution" is the cumulative outcome of repeated battles and dreaming passes acting on both node weights and edge weights. Nodes connecting outgroup members to attributes like *human*, *innocent*, *suffering* lose battles, have their node weights suppressed below the thinking threshold, and are subsequently seeded less often during dreaming. Their edge weights to the dominant cluster decay from disuse. They become unreachable by conscious thought — but not absent.

An incoming experience (a face, a cry, a moment of unexpected kindness) may reach a suppressed node via a traversal during dreaming — arriving through a low-weight but intact edge from an active neighbor. This creates **local tension**: a briefly activated subgraph that is incompatible with the dominant cluster. Since the suppressed node's weight remains below the thinking threshold, this tension cannot trigger a battle. It can only influence dreaming — very slowly, pass by pass, fractionally raising the suppressed node's weight each time it is activated without being immediately re-suppressed.

---

## What to Build

A simulation running the three processes above on a seeded graph, where:

- Nodes carry **content**, **type** (`idea` / `memory`), **timestamp**, and **stability**
- **Dreaming** runs passively and continuously, augmenting compatible nodes and drifting isolated ones toward forgetting
- **Thinking** actively generates new idea nodes, and triggers battles when conflicting ideas are encountered — suppressing losers without deleting them
- **Learning** injects external events as memories, triggering confabulation when causal gaps cannot be bridged naturally
- **Suppression cost** is readable directly from the graph: the aggregate node weight of all latent dissenters — nodes suppressed below the thinking threshold but not yet forgotten. No separate counter needed; the graph encodes it structurally

---

## Key Question for the Model

> Is there a threshold at which suppression becomes unsustainable — and what does "collapse" look like?

Sudden deradicalization? Breakdown? Doubling down?

---

## Possible Tools

- **Semantic embedding space** (e.g. word2vec / LLM latent space) as the underlying geometry
- **Ideological distortion** modeled as a learned transformation applied over a "ground truth" embedding layer that remains intact beneath it

---

## Key Implication

The model predicts that *more* extreme ideologues may carry *more* internal load, not less — because the gap between suppressed reality and constructed worldview is wider. That maps onto the PTSD-adjacent observation cleanly: the cost of maintenance scales with the degree of distortion required.
