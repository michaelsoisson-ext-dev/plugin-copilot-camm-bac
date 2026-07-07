---
name: ddd-glossary
description: 'Extract and formalize domain terminology (DDD) from the current project,  session or memory into a consistent glossary, saved to a local file. Use this skill to  scans for domain terms, resolves ambiguities, proposes canonical names, and writes a living glossary'
---

# Domain Language Glossary

It scans for domain terms, resolves ambiguities, proposes canonical names, and writes a living glossary to `.ai-camm-bac/explore/DOMAIN_GLOSSARY.md` in the working directory.

## Constraints

- _DO_ Use one preferred term per concept.Only include domain terms
- _DO_ Be opinionated. When multiple terms exist for the same concept, pick one canonical word and move others into “aliases to avoid”.
- _DO_ Keep definitions short, precise, and domain-focused.
- _DO_ Add examples when the meaning may be ambiguous.
- _DO_ Update the glossary whenever the domain changes.
- _DO_ Call out conflicts. Ambiguous or overloaded terms go into “Flagged ambiguities” with a clear recommendation.

## Operational Workflow

1. **Scan the project and context**
    - Read the current conversation, plus any attached docs or PRDs, for **domain-relevant** nouns, verbs, and concepts.
    - Ignore generic technical terms (array, function, endpoint) unless they carry domain meaning here.

2. **Detect language problems**

    Identify and note where:
    - The **same word** is used for different concepts (ambiguity).
    - **Different words** are used for the same concept (synonyms).
    - Terms are vague, overloaded, or inconsistently applied.

3. **Propose a canonical glossary**
    - Choose **one canonical term per concept** and be opinionated about names.
    - For each term, write a **tight, one‑sentence definition** that says what it _is_, not how it’s implemented.
    - List “aliases to avoid” so everyone sees which terms should be retired in this context.

4. **Group terms into sections**
    - Group related terms under headings (e.g. “Order lifecycle”, “People”, “Billing”, “Content model”).
    - Each group gets its own Markdown table with columns: `Term`, `Definition`, `Aliases to avoid`.
    - If everything belongs to a single cohesive cluster, one table is fine—don’t force fake groupings.

5. **Describe relationships**
    - Add a “Relationships” section that describes how the key terms relate to each other in plain language.
    - Use bold term names and, where obvious, simple cardinalities (e.g. “An **Order** has many **Line items**”).

6. **Write an example dialogue**
    - Add a short example conversation (3–5 exchanges) between a developer and a domain expert.
    - Use the canonical terms consistently and show how they interact in a realistic scenario.
    - Use this dialogue to clarify boundaries between easily-confused terms.

7. **Write or update `DOMAIN_GLOSSARY.md`**
    - If it exists, read it first, merge in new understanding, and update it in place.
    - writes a living glossary to `.ai-camm-bac/explore/DOMAIN_GLOSSARY.md` in the working directory.
      `DOMAIN_GLOSSARY.md` should roughly follow [this pattern](references/ddd-glossary-template.md)

## Re-running

When invoked again in the same repo or conversation:

1. Read the existing `DOMAIN_GLOSSARY.md`.
2. Merge in new terms introduced since the last run.
3. Update definitions where understanding has clearly evolved.
4. Mark changed entries with “(updated)” and new entries with “(new)”.
5. Add any new ambiguities you’ve observed to “Flagged ambiguities”.
6. Refresh the example dialogue so it reflects the latest terms and relationships.
