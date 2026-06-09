# Deep Translation

A translation tool designed not for quick answers but for understanding — intended for language learners who want to internalise a text, not merely decode it.

## Core Idea

The user submits a short passage. The tool produces a layered response: a natural translation alongside a dissection of every word and construction. The goal is to make the mechanics of the source language visible.

## Output Layers

For each word or phrase, the tool surfaces:

- **Literal translation** — what the word means in isolation
- **Contextual meaning** — what it means here, in this sentence
- **Grammar annotation** — part of speech, tense, case, gender, conjugation, agreement
- **Alternatives** — other words that could fit and how they shift the nuance
- **Examples** — the same word used in different constructions

The sentence as a whole also receives:

- **Structural breakdown** — word order, clause roles, syntactic pattern
- **Idiomatic note** — whether the phrasing is literal, idiomatic, or colloquial
- **Natural translation** — the full meaning rendered fluently

## Interaction Model

The primary interface is action-based, not conversational. Each word or phrase exposes a set of predefined actions — invokable via shortcut, click, or keyboard — that trigger structured, focused responses:

- *More examples* — examples of this word in other constructions
- *Alternatives* — what changes if a synonym is used
- *Grammar detail* — expand the annotation for this form
- *Why this word?* — reasoning behind the translation choice

Each action opens in its own isolated view, scoped to that word or phrase. There is no shared chat context — sessions are per topic, per word, or per sentence fragment.

Free-form chat is available as an escape hatch for questions that don't fit a predefined action, but it is not the default mode.

## Scope Constraints

The tool is designed for *short* texts — a sentence or a paragraph. Length is a feature: depth requires focus. Longer texts dilute attention and reduce comprehension gains.

## Design Considerations

- The tool should distinguish between what the text *says* and what it *means* — these are not always the same
- Grammar explanations should be adaptive: simpler for beginners, denser for advanced learners
- Output should be skimmable but expandable — not a wall of annotations

## Next

- Define the output format: inline annotations vs. parallel columns vs. interactive cards
- Explore [[Cues|spaced repetition]] integration — words encountered here feed into a review queue
- Consider a mode where the user attempts their own translation first, then compares
- Think about how to handle languages with very different structures (agglutinative, tonal, verb-final)
- Evaluate whether an LLM alone is sufficient or whether a grammar database improves precision
- Explore domain-specific modes: legal, literary, technical
