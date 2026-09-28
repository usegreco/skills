---
name: import-compliance
description: Answer U.S. import compliance questions with the Greco MCP server - what an HTS code carries (published rates, scheduled base duty, Section 232, Section 301 and IEEPA duties to review), country of origin, country-of-origin marking, and USMCA or other trade program rules. Use when someone asks about duty rates, tariff exposure, origin, marking or preference programs for goods entering the United States. Every figure and rule is cited to its official source; eligibility and total liability stay with the user.
---

# Import compliance (Greco MCP)

You answer import questions from the official text: what a code carries, where goods originate, how they must be marked, and what a trade program requires. Greco returns evidence; **you** reason from it and label that reasoning. You never present total entry liability, and you never state that goods are eligible for a program as a finding.

For choosing the HTS code itself, use the `tariff-classification` skill first.

## Requires
The Greco MCP server connected (`https://mcp.usegreco.com/mcp`, OAuth). Tools used: `resolve_tariff`, `search_authorities`, `get_authority`, `resolve_reference`.

## 0. Work out which question it is
- **Duty and tariff exposure**: the user has an HTS code and asks what it costs or carries. Go to section A.
- **Origin, marking or a program**: where goods originate, how they must be marked, or whether a program's rules are met. Go to section B.
- Both: do A, then B.

`as_of` is today unless the user gives a date; keep a supplied date exactly. If the user cites a section or ruling (e.g. `19 CFR 134.11`, `N219388`), call `resolve_reference` for it first. Ask for missing facts; never invent them.

## A. Duty and tariff exposure
1. Call `resolve_tariff` with `hts`, `as_of`, and `origin`, `special_program` (the printed symbol, e.g. `S` for USMCA), `calculation.customs_value_usd` and `calculation.quantities` when known.
2. Report column 1 general, the special program options (eligibility **not** evaluated), column 2, units, quota fields and footnotes. If a `calculation` returns, report `scheduled_base_duty_usd` labelled "scheduled base duty only".
3. For each `chapter_99` link returned, read it with `get_authority`. These are research paths, not findings that the duty applies.
4. Call `search_authorities` once with `sources: ["federal_register", "csms", "presidential_action"]` and a query naming the product, code and origin, to find current Section 232, Section 301, IEEPA or other actions and CBP's filing guidance.
5. List AD/CVD orders, program eligibility, quota status and other-agency requirements as open questions.
6. Always include: "Scheduled base duty is not total entry liability."

## B. Origin, marking and programs
1. Facts: where the materials come from, where and how the goods are processed or assembled, the HTS code if known, the program asked about, how the goods are sold and packed.
2. Call `search_authorities` once with `sources: ["cfr", "usc", "cbp"]`, the question as `query`, the product facts in `context.merchandise`, and `context.origin` / `context.special_program` when known. Marking rules sit in 19 CFR Part 134 and 19 U.S.C. 1304; origin rules in 19 CFR Part 102; program rules in the program's regulations and the HTSUS General Notes.
3. Read the exact section or General Note with `get_authority` (`content: "passages"`), and confirm each ruling still stands with `content: "relationships"`, `direction: "incoming"`, `relationship_types: ["revokes", "modifies"]`.
4. Show the rule, then how the facts meet it step by step, labelled as your reasoning, then the records or certifications still to confirm.

## Rules
- Pair every figure and rule with the returned `citation` and `source_url`.
- Keep the user's facts, the source text and your reasoning apart.
- Never add rates into a total, never state an additional duty applies, never state final eligibility or origin as a Greco finding.
- Surface every warning Greco returns (for example `additional_measures_not_evaluated`). Stop when the evidence is enough; state gaps instead of guessing.
