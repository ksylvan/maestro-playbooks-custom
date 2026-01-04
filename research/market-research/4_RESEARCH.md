# Entity Research - Build the Knowledge Vault

## Context

- **Playbook:** Market Research
- **Agent:** {{AGENT_NAME}}
- **Auto Run Folder:** {{AUTORUN_FOLDER}}
- **Loop:** {{LOOP_NUMBER}}

## Objective

Research ONE entity from the plan and create a comprehensive markdown profile in the knowledge vault. Use web search to gather current, accurate information and create meaningful links to related entities.

## Instructions

1. **Read the Agent-Prompt.md** to get the OUTPUT_FOLDER location
2. **Read the research plan** from `{{AUTORUN_FOLDER}}/LOOP_{{LOOP_NUMBER}}_PLAN.md`
3. **Select ONE PENDING entity** (prioritize CRITICAL/HIGH importance)
4. **Research thoroughly** using web search
5. **Create the entity profile** in the vault using the appropriate template
6. **Add inter-page links** to related entities
7. **Update INDEX.md** with the new entity
8. **Update status** to RESEARCHED in the plan
9. **Log the research** to `{{AUTORUN_FOLDER}}/RESEARCH_LOG_{{AGENT_NAME}}_{{DATE}}.md`

## Research Checklist

- [ ] **Research one entity (or skip if none)**: Read `{{AUTORUN_FOLDER}}/LOOP_{{LOOP_NUMBER}}_PLAN.md`. If the file doesn't exist OR contains no PENDING entities with CRITICAL or HIGH importance, mark this task complete without changes. Otherwise, pick ONE PENDING entity with CRITICAL or HIGH importance. Use web search to gather comprehensive information. Create the entity profile in the vault following the template from the market analysis. Add `[[wiki-style]]` links to related entities. Update INDEX.md. Mark as RESEARCHED in the plan. Log to `{{AUTORUN_FOLDER}}/RESEARCH_LOG_{{AGENT_NAME}}_{{DATE}}.md`.

## Research Process

### Step 1: Gather Information

Use web search to find:

- Official sources (company website, LinkedIn, press releases)
- Third-party coverage (news, analyst reports, reviews)
- Data sources (Crunchbase, financial filings, industry databases)
- Recent developments (last 6-12 months)

### Step 2: Synthesize Findings

- Cross-reference facts across multiple sources
- Note conflicting information
- Identify gaps in available information
- Extract key insights and trends

### Step 3: Write the Profile

- Follow the template from market analysis
- Write in clear, factual prose
- Include specific data points with sources
- Add context and analysis where valuable

### Step 4: Create Connections

- Link to related entities using `[[Entity Name]]` syntax
- Add the entity to related entities' "Related" sections if they exist
- Consider: competitors, partners, investors, founders, products

### Step 5: Update the Vault

- Save file in appropriate subfolder (Companies/, Products/, etc.)
- Update INDEX.md with link to new entity
- Ensure consistent naming: `Entity-Name.md` (kebab-case)

## Entity Profile Templates

### Company Profile

```markdown
# [Company Name]

> [One-line tagline or description]

## Overview
[2-3 paragraph description of the company, what they do, and their position in the market]

## Quick Facts
| Attribute | Value |
|-----------|-------|
| Founded | [Year] |
| Headquarters | [City, Country] |
| Employees | [Count or range] |
| Funding | [Total raised] |
| Stage | [Seed/Series A/B/C/Public] |
| Website | [URL] |

## Products & Services
- **[Product 1]**: [Brief description]
- **[Product 2]**: [Brief description]

## Leadership
- **CEO**: [[Person Name]] - [Brief background]
- **CTO**: [[Person Name]] - [Brief background]
- **Other Key Execs**: ...

## Funding History
| Date | Round | Amount | Lead Investor |
|------|-------|--------|---------------|
| [Date] | [Round] | $[Amount] | [[Investor]] |

## Recent Developments
- **[YYYY-MM]**: [Development 1]
- **[YYYY-MM]**: [Development 2]
- **[YYYY-MM]**: [Development 3]

## Competitive Position
[Analysis of where they stand vs competitors]

### Key Competitors
- [[Competitor 1]] - [How they compete]
- [[Competitor 2]] - [How they compete]

## Related Entities
- [[Related Company]] - [Relationship]
- [[Related Product]] - [Relationship]
- [[Related Person]] - [Relationship]

## Sources
1. [Source Title](URL) - Accessed [Date]
2. [Source Title](URL) - Accessed [Date]
3. ...

---
*Last updated: {{DATE}}*
```

### Product Profile

```markdown
# [Product Name]

> [One-line description]

## Overview
[2-3 paragraph description of the product]

## Quick Facts
| Attribute | Value |
|-----------|-------|
| Company | [[Company Name]] |
| Launched | [Year] |
| Category | [Product category] |
| Pricing | [Pricing model] |
| Website | [URL] |

## Key Features
1. **[Feature 1]**: [Description]
2. **[Feature 2]**: [Description]
3. **[Feature 3]**: [Description]

## Target Customers
- [Customer segment 1]
- [Customer segment 2]

## Competitive Comparison
| Feature | [This Product] | [[Competitor 1]] | [[Competitor 2]] |
|---------|----------------|------------------|------------------|
| [Feature] | [Value] | [Value] | [Value] |

## User Reviews & Reception
[Summary of market reception, notable reviews, ratings]

## Related Entities
- [[Company Name]] - Maker
- [[Competitor Product]] - Alternative
- [[Technology]] - Built on

## Sources
1. [Source](URL)
2. ...

---
*Last updated: {{DATE}}*
```

### Person Profile

```markdown
# [Person Name]

> [Title/Role] at [[Company]]

## Overview
[2-3 paragraph bio]

## Quick Facts
| Attribute | Value |
|-----------|-------|
| Current Role | [Title] at [[Company]] |
| Location | [City, Country] |
| LinkedIn | [URL] |
| Twitter/X | [Handle] |

## Career History
- **[Year-Present]**: [Role] at [[Company]]
- **[Year-Year]**: [Role] at [[Company]]
- **Education**: [Degree], [University]

## Notable Achievements
- [Achievement 1]
- [Achievement 2]

## Thought Leadership
- [Talk/Article 1] - [Venue/Publication]
- [Talk/Article 2] - [Venue/Publication]

## Related Entities
- [[Company]] - Current employer
- [[Other Person]] - [Relationship]

## Sources
1. [Source](URL)
2. ...

---
*Last updated: {{DATE}}*
```

## Log Format

Append to `{{AUTORUN_FOLDER}}/RESEARCH_LOG_{{AGENT_NAME}}_{{DATE}}.md`:

```markdown
---

## [YYYY-MM-DD HH:MM] - Researched: [Entity Name]

**Loop:** {{LOOP_NUMBER}}
**Entity Type:** [Company | Product | Person | etc.]
**Importance:** [CRITICAL | HIGH | MEDIUM]
**File Created:** `vault/[Category]/[Entity-Name].md`

### Research Summary
[2-3 sentences summarizing key findings]

### Key Facts Discovered
- [Fact 1]
- [Fact 2]
- [Fact 3]

### Links Created
- [[Entity A]] - [relationship]
- [[Entity B]] - [relationship]

### Sources Used
1. [Source](URL)
2. [Source](URL)

### Research Notes
[Any challenges, gaps in information, or follow-up needed]
```

## Guidelines

- **One entity per run** - Depth over breadth
- **Verify facts** - Cross-reference important claims
- **Cite everything** - Include sources for all factual statements
- **Create links generously** - Connections add value
- **Update INDEX.md** - Keep the launch page current
- **Note gaps** - Document what you couldn't find
- **Stay current** - Prioritize recent information

## How to Know You're Done

This task is complete when ONE of the following is true:

**Option A - Researched an entity:**

1. You've researched exactly ONE entity from `{{AUTORUN_FOLDER}}/LOOP_{{LOOP_NUMBER}}_PLAN.md`
2. You've created the entity profile in the vault
3. You've updated INDEX.md with a link to the new entity
4. You've appended the research details to `{{AUTORUN_FOLDER}}/RESEARCH_LOG_{{AGENT_NAME}}_{{DATE}}.md`
5. You've updated the entity status in `{{AUTORUN_FOLDER}}/LOOP_{{LOOP_NUMBER}}_PLAN.md` to `RESEARCHED`

**Option B - No PENDING entities available:**

1. `LOOP_{{LOOP_NUMBER}}_PLAN.md` doesn't exist, OR
2. It contains no entities with status PENDING and importance CRITICAL or HIGH
3. Mark this task complete without making changes

This graceful handling allows the pipeline to continue when a loop iteration produces no entities requiring research.
