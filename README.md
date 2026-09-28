# Greco skills

Customs and trade compliance skills for Claude and other AI agents. Each skill answers from primary US sources through [Greco MCP](https://usegreco.com/mcp) and cites them.

- **tariff-classification**: research an HTS classification from product facts, with headings, notes, GRIs and CBP rulings cited.
- **import-compliance**: duty rates, Section 232, 301 and IEEPA duties, country of origin, marking and USMCA or other programs.
- **export-compliance-research**: the Commerce Control List entries and EAR provisions that bear on an item or an export.
- **sanctions-list-lookup**: whether a name appears on OFAC's SDN or Consolidated lists, with the listing entries cited.

## Install

```sh
npx skills add usegreco/skills -g
```

One skill only:

```sh
npx skills add usegreco/skills --skill tariff-classification -g
```

The skills need Greco connected to your assistant:

```sh
claude mcp add --transport http greco https://mcp.usegreco.com/mcp
```

Every skill works on the Free plan. More at [usegreco.com/mcp/skills](https://usegreco.com/mcp/skills).
