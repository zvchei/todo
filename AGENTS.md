# Agent Instructions for ToDo Repository

## Repository Overview

Research and ideation documentation repository for capturing and developing ideas. All content is Markdown, organized as a flat mesh with cross-linking between documents.

**Type**: Documentation
**License**: CC0 1.0 Universal
**Tools**: Obsidian (local), Git (version control)

## Document Structure

- **File naming**: Descriptive titles (e.g., `Self-modifying agent.md`, `Wargame.md`)
- **Organization**: Flat structure with markdown cross-links between related concepts
- **Content pattern**:
  1. Core concept
  2. Design details
  3. Next steps/tasks/ideas

## Editing Guidelines

- **Preserve structure**: Maintain document style and cross-link conventions
- **Include next steps**: Use "Next" section with bullet points for next steps, open questions, and further directions
- **Cross-reference**: Link related concepts to build the mesh
- **Commit messages**: Descriptive and explain reasoning
- **No tracking files**: Don't create markdown files for planning or notes—use git history instead
- **Research methodology**: Document both findings and approach when exploring ideas

## Git Guidelines

- **Backup before rewriting history**: Before any operation that could destroy or alter existing commits — such as rebase, reset, squash, amend, or force push — create a local backup branch (`git branch backup/<description>`) first. Do not push it.

## Working Principles

- Concepts don't need to be complete; document thinking as it develops
- Each change should meaningfully advance a concept or create new ideas
- Keep previous iterations and exploratory work; preserve idea evolution
- Connect related ideas through cross-linking
- Explain reasoning in commit messages and inline comments
