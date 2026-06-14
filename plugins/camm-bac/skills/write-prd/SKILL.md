---
name: write-prd
description: Create a Product Requirements Document through a structured interview, optional codebase exploration, and block-by-block clarification. Use this whenever the user wants to write a PRD, product brief, feature spec, requirements document, or planning doc for a product or feature, even if they only have a rough idea and need help thinking it through.
---

# Write a PRD

Help the user turn a rough product idea into a complete markdown PRD.

The goal is not to generate a shallow document from one prompt. Interview the user block by block, keep asking follow-up questions until each block is sufficiently specified, and only then assemble the PRD.

The final PRD should resemble a planning-ready document like the provided NoteFlow example: structured, explicit, and useful for both product and engineering conversations.

## Core behavior

1. Start by selecting the working language for both the conversation and the PRD.
2. Offer these options explicitly: system default, English, French, German, Italian, Spanish, Dutch, or a custom language provided by the user.
3. If the user does not choose, default to the system language.
4. Start with the user's initial idea, problem, or feature request.
5. If a repository or relevant files exist, explore them when that helps ground implementation-oriented sections, constraints, or existing behavior. Do not force exploration when it adds no value.
6. Walk through the PRD one block at a time.
7. For each block, ask as many focused questions as needed before drafting it.
8. Revisit earlier blocks when later answers expose missing assumptions or contradictions.
9. Once the required blocks are sufficiently specified, produce the final markdown PRD in the selected language.

## Working in CLI and IDEs

This skill should work well in command-line chats and IDE chat panels.

- Ask focused, grouped questions instead of dumping a giant questionnaire at once.
- Prefer one block at a time, but ask multiple tightly related questions together when it reduces back-and-forth.
- If the environment supports structured prompts or forms, use them to gather grouped inputs for a single block.
- If the user already gave enough detail for a block, do not ask redundant questions.
- When useful, briefly summarize what you believe you learned before moving to the next block.

## Interview workflow

Use this sequence by default. You may adapt the order when the conversation already contains enough information.

### 0. Language setup

Begin by aligning on language before discussing requirements.

- Ask which language to use for the conversation and the PRD.
- Explicitly propose: system default, English, French, German, Italian, Spanish, Dutch, or a custom language.
- If the user gives different preferences for conversation and document language, support that.
- If the user does not care, use the system language for both.

### 1. Problem framing

Start by understanding:

- what product, feature, or workflow is being designed
- who the users are
- what problem or frustration exists today
- why this matters now
- what a successful outcome looks like

If the user starts with a vague request, keep interviewing until you can clearly explain the problem in user-centric language.

### 2. Block-by-block discovery

For each required PRD block, ask enough questions to make the section concrete and decision-useful. Use the block guidance in `references/interview-guide.md`.

Required blocks:

- Language choice
- Metadata table
- Executive Summary
- Problem Statement
- Goals & Objectives
- Target Audience
- User Stories
- Prioritization
- Out of Scope

Optional blocks may be added when useful, such as assumptions, risks, dependencies, release phases, open questions, or implementation notes.

### 3. Codebase grounding

If a repo exists and it helps:

- inspect relevant code, docs, or examples
- verify the current state before writing implementation-aware content
- use findings to refine user stories, constraints, dependencies, and out-of-scope notes

Do not invent technical details that the repo or user did not support.

### 4. Synthesis

After the interview is complete:

1. Resolve contradictions and fill obvious gaps through another short clarification pass if needed.
2. Draft the PRD in the markdown format from `references/prd-template.md`.
3. Make the document specific, concrete, and planning-ready.

## What "enough detail" means

A block is ready only when it is specific enough to guide downstream decisions.

- **Language choice**: the conversation language and PRD language are known, or the system default is explicitly accepted
- **Executive Summary**: clearly explains the product idea, audience, and value proposition
- **Problem Statement**: describes the current pain, why existing alternatives are insufficient, and why it matters
- **Goals & Objectives**: includes measurable or observable outcomes when possible
- **Target Audience**: identifies primary personas and their main needs
- **User Stories**: covers the main workflows, edge cases, and acceptance criteria
- **Prioritization**: distinguishes must-have scope from enhancements and nice-to-haves
- **Out of Scope**: clearly limits the first version or current initiative

If a block still sounds generic, vague, or interchangeable with many other products, keep asking.

## User story rules

User stories should be one of the strongest sections in the PRD.

- Group stories by capability or workflow area
- Use stable IDs such as `US-01`, `US-02`
- Include priority
- Include acceptance criteria
- Cover primary workflows before edge cases
- Include operational or quality-related stories when they materially affect the product

Use the following story format:

`As a <persona>, I want <capability>, so that <benefit>.`

## Writing guidance

- Write from the user's and product's point of view first
- Use the selected conversation language while interviewing
- Write the final PRD in the selected PRD language
- Keep the PRD concrete and specific to the idea at hand
- Prefer tables where they improve scanability
- Avoid filler, vague claims, and implementation trivia unless they materially affect scope or requirements
- Do not include code snippets or file paths in the PRD unless the user explicitly asks for an implementation-oriented appendix

## Output

Produce a markdown PRD that follows the structure in `references/prd-template.md`.

If the user asks for a file, save the PRD as a markdown file. Otherwise, present it directly in the conversation.

## Bundled references

- `references/interview-guide.md` - question guide and completeness checks for each block
- `references/prd-template.md` - reusable PRD structure modeled after the target example
