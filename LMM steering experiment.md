# LMM steering experiment

## Concept

Learn the encoding at a target layer by mapping steering vectors to the vector difference between expected and actual outputs. Use this mapping to construct effective steering vectors from user prompts.

## Implementation Strategy

### Setup
- Modify a model to accept steering vectors at target layers.
- Implement a flexible mechanism that allows testing different steering injection timing strategies:
  - Add the steering nudge to the first N tokens.
  - Apply full strength at the start and decay over subsequent tokens.
  - Or in reverse: start weak and increase strength over tokens.
- The shape of passing vectors cannot be changed; the passing vectors must be rotated by the steering vector.

### Data Collection
- Generate random steering vectors and inject them at chosen layers and timing strategies.
- Record outputs (tokens and vectors) from the steered model and from the baseline model (no steering).
- Compute vector diffs between baseline and steered outputs.
- Map diffs to nearest corresponfing tokens to identify the steering effect.
- Build a dataset: (steering vector, strategy, layer) → (output diff, apparent steering tokens).

### Mapping Learning
- Train a model to predict the backward mapping (steering tokens) → (steering vectors).
- Experiment with different model architectures (e.g., linear regression, MLP, transformer) to find the best mapping performance.
- Construct a steering token computer, assuming the tokens map to a smooth steering vector space.