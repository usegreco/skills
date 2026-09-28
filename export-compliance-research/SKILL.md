---
name: export-compliance-research
description: Find the Export Administration Regulations (15 CFR Parts 730-774) and Commerce Control List text relevant to an item or transaction using the Greco MCP server. Use when someone asks which CCL entries or EAR provisions to review for an export. Returns candidate entries and passages with citations for the user's own review; it never states an ECCN, EAR99 status, license requirement or license exception.
---

# Export compliance research (Greco MCP)

You find and quote the EAR and Commerce Control List text an export question turns on. The export classification, EAR99 status, license requirement and any license exception are the user's decisions. This skill never states them, even when the text looks conclusive.

## Requires
The Greco MCP server connected (`https://mcp.usegreco.com/mcp`, OAuth). Tools used: `search_authorities`, `get_authority`, `resolve_reference`.

## 1. Facts
The item (what it is, technical parameters, performance figures, software or technology involved), the destination, the end user and the end use. Ask for what is missing; never invent parameters. `as_of` is today unless the user gives a date.

## 2. Search
Call `search_authorities` once with:
- `query`: the item and its key technical parameters
- `sources`: `["ccl", "cfr"]` (`cfr` returns Title 15 EAR text as well as Title 19)
- `context.merchandise.description` and other facts supplied

If the user names an ECCN or EAR section (e.g. `3A001`, `15 CFR 740.17`), call `resolve_reference` first.

## 3. Read the entries
Use `get_authority` (`content: "passages"`) for each candidate CCL entry's full text: its items paragraph, reasons for control, and related definitions in 15 CFR Part 772.

## 4. Answer
1. **Candidate CCL entries to review**, each with the parameter text that makes it a candidate, cited.
2. **EAR provisions that bear on the transaction** (for example the Commerce Country Chart, end-use or end-user rules), cited.
3. **What the user must still decide**: the ECCN or EAR99 status, license requirement and license exception.
4. Every passage with the returned `citation` and `source_url`.

## Rules
- Never write "this item is classified as", "no license is required" or "EAR99" as a conclusion. Absence of a match is not EAR99.
- Do not use this skill for sanctions questions; see `sanctions-list-lookup`.
- Surface every warning Greco returns; state gaps instead of guessing.
