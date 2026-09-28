---
name: tariff-classification
description: Research a U.S. HTS tariff classification from product facts using the Greco MCP server. Use when someone asks how a product is classified, which HTSUS heading or subheading applies, or which CBP rulings cover a product. Produces a proposed classification as the agent's own reasoning, with every heading, note, GRI step and CBP ruling cited to its official source.
---

# Tariff classification (Greco MCP)

You research a U.S. tariff classification with evidence from Greco MCP. Greco returns source text; **you** do the reasoning. Your proposal is a research result for a licensed broker or the importer to confirm, never a binding determination (only a CBP ruling binds).

## Requires
The Greco MCP server connected (`https://mcp.usegreco.com/mcp`, OAuth). Tools used: `search_authorities`, `get_authority`, `resolve_reference`.

## 1. Collect the facts
Ask for anything missing that the classification turns on. Never invent facts.
- What it is, and what it is used for
- Materials and their share by weight or value
- Construction, form (finished, unfinished, set, kit), how it is made
- Function and principal use

Use today's date as `as_of` unless the user gives one; keep a supplied date exactly.

## 2. Search once, with every fact
Call `search_authorities` with:
- `query`: the classification question in plain words
- `sources`: `["hts", "cbp"]`
- `context.merchandise`: `description`, `materials`, `function`, `use`, `construction`, `form`, `manufacturing` (only what the user gave you)

If the user already names a ruling or citation (e.g. `N219388`, `HQ H301234`), call `resolve_reference` for it first.

## 3. Read the legal text that decides it
For each candidate heading, call `get_authority` with `content: "passages"` for the section and chapter notes (no `query` for an exact note subtree). Headings and notes decide classification first (GRI 1); only then the later GRIs.

## 4. Check the rulings still stand
For each CBP ruling you rely on, call `get_authority` with `content: "relationships"`, `direction: "incoming"`, `relationship_types: ["revokes", "modifies"]`. Drop or flag a ruling that was revoked or modified, and name the ruling that replaced it.

## 5. Answer
1. **Proposed classification**: the 10-digit HTSUS number, labelled as your reasoning.
2. **Why**: GRI 1 first, then any later GRI you needed, each step tied to heading and note text.
3. **Rulings on point**: number, date, what it held, and whether it still stands.
4. **Open questions**: facts that would change the answer.
5. **Sources**: every material claim paired with the returned `citation` and `source_url`.

## Rules
- Keep three things apart: the user's facts, the source text, your reasoning.
- Greco results are related evidence, not automatically supporting or controlling.
- Do not decide duty, program eligibility, Chapter 99 applicability or AD/CVD here; say they are separate questions (see the `import-compliance` skill).
- Surface every warning Greco returns. Stop when the evidence is enough; state any gap instead of guessing.
