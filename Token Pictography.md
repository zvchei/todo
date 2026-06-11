# Token Pictography

Pick a tokenizer. Give every token a picture. Call it a writing system.

## Core Idea

LLM tokenizers split text into a fixed vocabulary of chunks — words, word-pieces, punctuation clusters, numbers. GPT-2 has ~50,000 of them; GPT-4 has ~100,000. The idea: drawing a small pictogram for each token, one symbol per chunk. Feed any text through the tokenizer, swap each token for its picture. The result is a "natural" working writing system, bootstrapped from a language model vocabulary.

## Drawing the Symbols

- The guiding principle: **each symbol looks like what the token means as a word.**
- If a tokenizer splits `toolbox` into `tool` + `box`, the workd could be written with two symbols — a `tool` followed by a `box`. Sub-word tokens often happen to be real words, so this works in most of the cases.
- For tokens that aren't words (`ization`, `##ing`, `▁un`), draw something abstract — a modifier mark — keeping it consistent wherever that fragment appears.
- Tokens that are purely structural — spaces, newlines, brackets — get minimal punctuation-style marks, usually the same as the genrally accepted.
- Code tokens (`def`, `return`, `=>`, `import`) are part of the vocabulary too, so the script writes code and prose in the same symbol set.

## Tokenizers to Start From

- **GPT-2** (~50k tokens) — smallest, most tractable for a hand-crafted pilot
- **cl100k_base** (GPT-3.5/4, ~100k) — broader, includes more languages and code
- **LLaMA 3** (~128k) — stronger multilingual coverage

## Handling multiple languages

- If the tokenizer includes multiple languages, different tokens may have the same symbol based on the meaning in the language they're used in.
- Tokens representing the same grammatical function across languages (like "the" in English and "le" in French) could share a symbol. Such symbol could be designed as a positioinaly independent in the pictogram, to even further condense the writing system. For example, a symbol for "the" could be a dot above the word, thus "the cat" and "котката" could both be written in the same way.

## Open Questions

- How do you handle tokens that mean nothing on their own? (`ization`, `##s`, `▁`)
- Does the script feel like reading, or like solving a puzzle? Does it matter?
- What happens when a tokenizer is updated?

## Next

- Try tokenising a short text (a poem, a recipe) and sketch pictograms for each token by hand
- Use an image model to generate a consistent glyph table for the top 500 GPT-2 tokens
- Build a simple converter: text → tokenize → render glyphs as an image
- Explore whether related tokens (`run`, `running`, `runner`) should share a visual root
- Connect to [[Deep Translation]] — showing tokenisation as pictograms could be a fun teaching tool
- Print a short rendered text as an art object
