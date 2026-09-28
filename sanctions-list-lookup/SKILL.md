---
name: sanctions-list-lookup
description: Look up a person, company or vessel name on OFAC's published Specially Designated Nationals (SDN) and Consolidated Non-SDN lists using the Greco MCP server. Use when someone asks whether a name appears on an OFAC list. Returns the matching listing entries with citations; it never clears an identity or concludes ownership, prohibition or licensing.
---

# Sanctions list lookup (Greco MCP)

You find whether a name appears in OFAC's published SDN and Consolidated Non-SDN listings and show the listing text. A listing match or its absence is **not** a clearance, an identity determination or a finding that a transaction is prohibited or allowed. Those decisions stay with the user and their compliance process.

## Requires
The Greco MCP server connected (`https://mcp.usegreco.com/mcp`, OAuth). Tools used: `search_authorities`, `get_authority`.

## 1. Inputs
The exact name, any known aliases or former names, and identifiers the user has (country, address, date of birth, registration or IMO number). `as_of` is today unless the user gives a date.

## 2. Search
Call `search_authorities` with `sources: ["ofac"]` and a `query` containing the name. Repeat once per alias or transliteration the user supplied. Do not invent spelling variants beyond what the user gave, and say which variants you did not search.

## 3. Read the listings
For each returned entry, call `get_authority` (`content: "passages"`) if you need the full listing: programs, aliases, addresses and identifiers.

## 4. Answer
1. **Listing entries returned**, each with list name, program tags, aliases and identifiers, cited.
2. **How each compares with the user's identifiers**: matching, conflicting or unknown, stated as a comparison, not a conclusion.
3. **What the lookup does not cover**: ownership and the 50 Percent Rule, non-OFAC lists, and whether the transaction is permitted.
4. Every entry with the returned `citation` and `source_url`.

## Rules
- Never write "cleared", "no sanctions risk", "not sanctioned" or "prohibited". Report what the listings say.
- No returned entry means "no listing entry was returned for the names searched", nothing more.
- Surface every warning Greco returns (for example `sanctions_not_evaluated`).
