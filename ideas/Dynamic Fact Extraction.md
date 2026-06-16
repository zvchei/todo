# Architectural Design: Dynamic Context Depletion for LLM Fact Extraction

## Abstract
The objective is the extraction of self-contained facts from a large text corpus. Supplying entire documents exceeds context windows, while static segmentation fractures multi-sentence propositions. This architecture employs a dynamic traversal and context depletion methodology. The LLM processes text, extracts facts, and actively removes the consumed text from its context window. When the context is fully depleted or reduced to unresolvable residue, the system utilizes tool calling to fetch the next sequential text segment.

## Definition of a Fact
Within this architecture, a *fact* is a logically complete and inseparable span of text that carries meaning. It is identified by a single *main* meaning—the one the extraction process is intended to capture. Natural language is polysemic, so a given span will frequently carry several overlapping meanings, but only this main meaning defines the boundaries of the fact. A fact is not bound to a single sentence; it may span several sentences when the meaning is distributed across them. Two operational criteria govern its boundaries: completeness and minimality. Completeness requires that every part necessary to preserve the main meaning be retained, so removing any constituent part destroys it. Minimality is the converse: if a part can be removed without damaging the main meaning, it was never necessary and does not belong in the fact.

## Text Preparation
The ingestion system preprocesses the source document utilizing Sentence Boundary Detection. This converts the raw text string into an indexed array of grammatically complete sentences. The backend provides the initial sequence of sentences to the inference engine to begin the extraction cycle.

## Fact Extraction and Context Depletion
The LLM operates as an iterative extraction engine with active pruning capabilities over its context buffer. Upon identifying an autonomous factual statement, the LLM outputs the fact and simultaneously deletes the corresponding source text from the active buffer. Text segments that serve as shared context or logical referents for subsequent, unextracted facts are retained within the buffer until all dependent propositions are resolved. This continuous depletion mechanism prevents context saturation and naturally isolates complex logical structures.

## Dynamic Context Expansion
The extraction-depletion cycle continues until the active buffer reaches one of two terminal states: complete consumption or unresolvable residue. Unresolvable residue occurs when the remaining text contains dangling modifiers, unresolved coreferences, or incomplete syllogisms requiring subsequent text to form a complete fact. Upon reaching either state, the LLM halts extraction and invokes the `request_next_sentences(count)` tool. The backend retrieves the specified number of subsequent sentences from the indexed source array, appends them to the remaining residue, and the LLM resumes the extraction cycle.
