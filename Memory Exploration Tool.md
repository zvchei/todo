# Memory Exploration Tool

## Introduction

The Memory Exploration Tool is a system whose central purpose is to facilitate the retrieval, recording, and long-term preservation of personal memories. Rather than treating memory as an incidental byproduct of journaling, the tool actively engages a wide array of retrieval techniques, then captures whatever surfaces into a personal archive — a *vault* — so that, over time, a user gradually builds a comprehensive record of their own existence.

Originally introduced as a sub-component of [The Library](The%20Library.md), the Memory Exploration Tool is described here as a standalone system, with focus on its conceptual model and the architectural patterns that support it.

## Vision

The tool is built on three premises:

1. **Memories fade unless actively excavated.** Most personal experiences are never re-examined and are eventually lost. Deliberate retrieval, repeated over time, recovers detail that passive recall cannot.
2. **Retrieval is a multi-modal process.** No single technique reaches every memory. Different methods — meditation, association, structured interrogation, scheduled review — each surface a different layer.
3. **A life is worth archiving.** The accumulated record is not just a backup; it is the user's personality's archive, a time capsule that preserves the texture of their existence and remains legible to themselves and, eventually, to others.

The tool does not aim to *interpret* a user's life. It aims to provide the conditions under which memories can be reliably surfaced and faithfully recorded.

## Core Concepts

### The Vault

The Vault is the durable archive. It stores memory entries together with the metadata accumulated around them: when the memory was recorded, which technique surfaced it, what emotional tone the user reported, who and what is referenced, and how it relates to other entries. The Vault is an implementation of a concept described in [The Library](The%20Library.md), specialized for the domain of personal memory. Alternatively, it can be realized as an interface to *The Library* itself.

Properties of the Vault:

* **Append-only by default.** A memory, once recorded, is not edited in place. Refinements, corrections, and re-tellings are stored as new entries linked to the original. Drift in recollection is itself information worth preserving.
* **Temporally indexed.** Entries are indexed both by *when the memory occurred* (as best the user can place it) and *when it was recorded*, allowing the archive to be navigated chronologically along either axis.
* **Long-lived.** The Vault is designed for decades, not sessions. Storage formats favor durability and portability over convenience.

### Memories vs. Sessions

A *session* is a single retrieval episode (a meditation, an interview, a free-association run). A *memory* is the content surfaced and recorded. One session may produce many memories; one memory may be revisited across many sessions. The tool tracks both, and the relationship between them.

## Retrieval Techniques

The tool exposes a suite of retrieval techniques. They are intentionally varied: each reaches a different kind of memory, and each has its own failure modes.

### Guided Meditation

LLM-driven guided meditation, delivered through text-to-speech, places the user in a contemplative state and directs attention toward a chosen period, place, or theme. Best suited for surfacing sensory and emotional detail that the user does not consciously hold in focus.

### Guided Active Imagination

A more directive variant of meditation: the system narrates a scenario or scene and invites the user to inhabit it. Useful for re-entering known memories with fresh attention, and for surfacing feelings or impressions attached to events that the user has previously only narrated factually.

### Free Association

The user is presented with a prompt — a "Word of the Day", an image, a date, a name — and records whatever surfaces in response, with minimal filtering. The NLP layer then mines the resulting stream for entities, themes, and connections to existing entries.

### Prompted Retrieval

Personal items and photos can be used as prompts, either by the user or by the system. The tool can analyze the content of photos to extract entities and themes, or plainly ask the user about the content and use the extracted information and their responses as retrieval cues and context for memory exploration.

### Interview and Interrogation

Structured questioning, conducted by the LLM. The system begins broadly ("tell me about that summer") and progressively narrows ("who else was there?", "what did the room smell like?", "what did you not say at the time?"). Interviews are particularly effective at extracting concrete detail and at exposing inconsistencies that warrant follow-up.

### Scheduled Retrieval

The tool maintains a schedule of prompts that bring the user back to specific topics, periods, or individual memories. Scheduling is adaptive: memories that resist recall are revisited more often; memories that have stabilized are spaced further apart. This is the mechanism by which the archive becomes systematic rather than opportunistic.

### Revisiting Old Memories

The user — or the system, on the user's behalf — surfaces previously recorded memories for re-examination. The user re-tells the memory; the new telling is stored alongside the old. Comparing tellings over time exposes what has shifted, what has solidified, and what new associations have formed.

### Multi-user Memories

The tool can be used collaboratively, with multiple users contributing memories about shared experiences. This is especially interesting when evalueating the same event from different perspectives, especially considering the tendency of memories to diverge over time.

### Multi-user Sharing

When sharing memories of a shared experience, the tool can surface the different perspectives in parallel, allowing users to see how their recollections align and diverge, eventually leading to better recall.

### Sensitive topics (trauma, loss, conflict, etc.)

Those are handled with care. The tool can detect when a user is reporting distress, and can adjust the session accordingly: slowing down, offering support, or switching to a different topic. Sensitive memories are not avoided, but they are approached with respect and heightened safety. The tool should never share a private memory and sensitive memories should automatically be flagged as such, unless the user explicitly opts in to sharing them.

## Background tasks

The tool performs a number of tasks asynchronously, periodically or after the user has finished a session:
* **NLP analysis.** Extracts entities, themes, emotional tone, and candidate links from newly recorded entries.
* **Linking.** Proposes links between new entries and existing ones, based on shared entities, themes, or temporal proximity.
* **Scheduling.** Updates the retrieval calendar based on new entries and the user's interaction with scheduled prompts.
* **Exporting.** Periodically creates a backup of the Vault in a durable, portable format. This could be implemented as a function of the Vault itself.
* **Analysis.** Periodically analyzes the archive as a whole, extracting high-level insights about the user's life: dominant themes, frequently mentioned people and places, emotional arcs, etc. These insights can be surfaced in the UI or used to inform retrieval scheduling.

## Architecture

The tool is organized around a number of cooperating components.

### Component Overview

...

## Digital Memorial Mode

A distinct mode of operation in which one or multiple users contribute memories *about another person* — typically someone who has died — into a shared Vault dedicated to that individual.

### Multi-Contributor Vaults

A memorial Vault accepts entries from many users, each clearly attributed. Every contributor retains authorship of their own entries; no contributor's recollection is overwritten or merged into anyone else's. The shared archive is the union of these individual perspectives, not a consensus document.

### Cross-Checking and Cross-Referencing

Because memories about the same person and the same events are recorded by different witnesses, the tool can:

* **Identify convergence.** When multiple contributors independently report the same detail, that detail is flagged as well-corroborated.
* **Surface divergence.** When accounts of the same event differ, the differences are made visible rather than reconciled away. Divergence is treated as a feature of remembered reality, not a defect.
* **Fill gaps.** Aspects of the subject's life remembered by only one contributor are preserved with the same weight as widely shared memories, ensuring rare recollections are not lost.

### Living Archive

A memorial Vault is not finalized. It remains open to new contributors and new entries indefinitely, allowing the portrait of the subject to deepen over years and across generations. The same retrieval techniques used in personal mode are available here, applied to the contributor's memories of the subject rather than of themselves.
