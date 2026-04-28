An agent that iteratively updates its own instructions to improve its performance.
* The agent has a SKILL and a tool for creating new python-based tools.
* The agent has a SKILL to create new skills.

## Evolutionary Skill Adaptation

### Mechanism Overview
A population-based optimization loop that evolves skills through targeted mutation, selection, and recombination. Skills are iteratively improved by addressing specific failure modes, then pruned based on overall performance.

### Process Flow

**Phase 1: Evaluation & Improvement Loop (repeat K times)**
1. Evaluate all skills in current population against test case corpus
2. For each skill that fails a test case:
   - Execute: `Improver(skill, error_context)` → new variant
   - Validator confirms change is minimal (diff check, AI comparison)
   - Add variant to population
3. Population now contains: originals + targeted variants (many variants per original)
4. Repeat from step 1 with expanded population

**Phase 2: Selection (Herd Thinning)**
- Evaluate entire final population against full test case corpus
- Compute composite performance score for each skill
- Rank by score
- Retain top N skills (e.g., top 20 absolute, not percentage), discard rest
- Bound population size to control resource cost

**Phase 3: Recombination**
- Crossover skill combines traits from top N performers
- Mix prompts, instruction sets, or decision rules
- Create new hybrid candidates from successful lineages

**Phase 4: Cycle Continuation**
- Survivors + recombined hybrids become new population for next round
- Repeat Phases 1–3
- Track lineage and performance history across all cycles
- Termination: convergence plateau, diversity collapse, or iteration budget

### Key Skills Required
- **Evaluator**: Scores skill on test cases, identifies failure modes
- **Improver**: Takes (skill, error_context) → returns minimally-modified skill variant
- **Validator**: Verifies changes are small (semantic/structural diff)
- **Crossover**: Merges top performers into hybrid skills
- **Case Generator** (optional): Expand test corpus to prevent overfitting

### Design Notes
- Problem-targeted variants (one per failure) avoid blind mutation
- K iterations of evaluation+improvement allow deep exploration
- Fixed-size thinning (top N) prevents population explosion
- Lineage tracking enables reversion and analysis of successful branches
- Parallel evaluation+improvement accelerates cycle time

### Open Questions
- **GAN-style coevolution**: Can evaluator, improver, and validator skills themselves be evolved adversarially? (evaluator refines grading, improver learns better mutations, validator catches overfitting)
- **Skill interdependencies**: How do we evolve dependent skills without cascading failures?
- **Convergence signals**: Plateau in top scores vs. diversity collapse vs. fixed iteration budget?
- **Test corpus evolution**: Should test cases themselves evolve to stay challenging (dynamic curriculum)?
