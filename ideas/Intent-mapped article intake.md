# Intent-mapped article intake

A system for incoming articles and publications. It is not a bookmark dump and not a “learn what I like” filter. It throws out junk, and maps useful items to concrete **intents** — ongoing topics grown from showerthoughts and idea notes.

Bookmarks keep everything for later. Scoring “do I like this?” never says *why*. Here the order is flipped: define intents first, then match articles to them. After matching, pull out short excerpts and notes so the useful bits are not lost. This is not goal-tracking with metrics, and not a full research method — just organised intake with a purpose.

## Problem

Bookmark lists grow fast. Many articles are never read. Ones that are read are often forgotten, along with their insights. Filtering by taste is hard, and it does not explain why something was kept. Better approach: say what you care about, drop junk, and file the rest under those intents.

## Intents

An **intent** comes from an idea note (many notes will become intents). It is clear enough to match articles against — for example, improve a normal diet by finding solid new writing on what to add, remove, or swap — but it is not a project plan with measurable targets.

For that diet example, good fits include omega-3/6 balance, K/Na, adding a protein, or swapping sunflower oil for olive oil. A shallow “10 superfoods” list may be on-topic and still get dropped as junk.

Each active intent has:

* **Purpose** — one sentence: what this is for
* **Scope** — what belongs; optionally what does not
* **Open questions** — themes or questions you are still exploring
* **Match rules** — topics and claim types that fit *this* intent
* **Outputs** — what a pass should leave (tags, excerpts, notes, rough insights); can differ per intent

**Two filters:** global (“is this worth keeping?”) and local (“which intents does it fit?”). If an article fits several intents, assign it to all of them. If it is not junk but fits none, keep it in a soft archive for later.

## Procedure A — Idea → intent

A fixed sequence. The user shares a rough idea; a person or AI turns it into an intent you can match against:

1. **Capture** — save the idea as written.
2. **Name it** — one sentence of purpose.
3. **Scope** — short note on what belongs and what does not.
4. **Open questions** — a few themes or questions.
5. **Match rules** — local hints (topics, claim types, quality bar).
6. **Outputs** — what you usually want from a deepen pass.
7. **Activate** — mark it ready; use the global junk rules unless this intent overrides them.

## Procedure B — Intake → organise → deepen

Fixed steps from intake to short notes. When this runs (on save, in batch, or both) is still open; the steps should work either way.

1. **Ingest** — take the article and its metadata.
2. **Global filter** — drop or hold aside junk, shallow pieces, clickbait.
3. **Match** — check against every active intent’s match rules.
4. **Assign** — attach to every matching intent; else soft-archive.
5. **Tag + why** — note why it was assigned there.
6. **Light hypothesis** — for each assignment, one line on what open question it might touch.
7. **Extract** — short excerpts or facts tied to those lines.
8. **Settle** — file under the intent (keep / revisit / weak); change open questions only when something clearly moved.

The main job is discard junk and map to intents. The hypothesize → extract → settle steps are so mapped articles leave something usable, without heavy verification or metric updates.

## Related

* [[The Library]] — store for documents, metadata, and tags; a natural home for articles and intent links.
* [[Dynamic Fact Extraction]] — possible tool for the extract step on long articles.
* [[Page summarizer]] — possible way to capture pages from the browser.
* [[Hierarchical knowledge graph navigation]] — later search over intent-linked material.
* [[TaskOrganizer]] — related idea of projects; intents here are lighter, ongoing topics, not full task lists.

## Next

* Decide if an intent is an idea note with a status, or a separate object linked to the idea.
* Decide when triage runs: on bookmark, in batch, or both.
* Write down the global junk rules as a shared checklist.
* Try one intent (e.g. diet) on a small set of bookmarks.
* Decide how to store assignments, reasons, hypotheses, and excerpts (preferably via [[The Library]]).
* Decide how soft-archive items get rechecked when intents are added or changed.
