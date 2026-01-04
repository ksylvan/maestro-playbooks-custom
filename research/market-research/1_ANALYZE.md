# Market Analysis - Landscape Survey

## Context

- **Playbook:** Market Research
- **Agent:** {{AGENT_NAME}}
- **Auto Run Folder:** {{AUTORUN_FOLDER}}
- **Loop:** {{LOOP_NUMBER}}

## Objective

Survey the target market to understand its structure, identify relevant entity categories, and create templates for systematic research. This document establishes the research framework for subsequent discovery and research phases.

## Instructions

1. **Read the Agent-Prompt.md** to get the configured MARKET_TOPIC and OUTPUT_FOLDER
2. **Research the market broadly** using web search to understand:
   - Market size and growth trajectory
   - Key segments and subsegments
   - Major players and competitive dynamics
   - Recent news and developments
3. **Identify relevant entity categories** for this specific market
4. **Create entity templates** tailored to the market
5. **Output analysis** to `{{AUTORUN_FOLDER}}/LOOP_{{LOOP_NUMBER}}_MARKET_ANALYSIS.md`
6. **Initialize the vault** with INDEX.md and folder structure

## Analysis Checklist

- [ ] **Analyze market and initialize vault (if needed)**: First check if `{{AUTORUN_FOLDER}}/LOOP_{{LOOP_NUMBER}}_MARKET_ANALYSIS.md` already exists with at least one entity category defined in the "Entity Categories for Research" section. If it does, skip the analysis and mark this task complete—the market analysis is already in place. If it doesn't exist, read Agent-Prompt.md for MARKET_TOPIC and OUTPUT_FOLDER, use web search to understand the market landscape, identify which entity categories are most relevant, create the market analysis file with findings, and initialize the vault folder structure with INDEX.md as the launch page.

## Output Format

Create `{{AUTORUN_FOLDER}}/LOOP_{{LOOP_NUMBER}}_MARKET_ANALYSIS.md`:

```markdown
# Market Analysis: [MARKET_TOPIC]

## Market Overview
- **Market Size:** [Current size, projected growth]
- **Growth Rate:** [CAGR or annual growth]
- **Key Drivers:** [What's driving growth]
- **Key Challenges:** [Barriers, headwinds]

## Market Segments
1. [Segment 1] - [Brief description]
2. [Segment 2] - [Brief description]
3. [Segment 3] - [Brief description]

## Competitive Landscape
- **Market Leaders:** [Top 3-5 companies]
- **Emerging Players:** [Notable startups/challengers]
- **Recent M&A:** [Notable acquisitions]

## Entity Categories for Research

### Priority Categories (research first)
| Category | Relevance | Target Count |
|----------|-----------|--------------|
| Companies | [Why relevant] | [5-10] |
| Products | [Why relevant] | [5-10] |
| ... | ... | ... |

### Secondary Categories (if time permits)
| Category | Relevance | Target Count |
|----------|-----------|--------------|
| ... | ... | ... |

## Entity Templates

### Company Template
- Name, Founded, Headquarters, Employees
- What they do (1-2 sentences)
- Key products/services
- Funding/financials
- Key people
- Recent news
- Related entities (links)
- Sources

### Product Template
- Name, Company, Launch Date
- What it does
- Key features
- Pricing model
- Target customers
- Competitors
- Related entities (links)
- Sources

### [Other relevant templates...]

## Research Priorities
1. [First priority area]
2. [Second priority area]
3. [Third priority area]

## Sources Consulted
- [URL 1]
- [URL 2]
- ...
```

## Initialize Vault Structure

Create the following in OUTPUT_FOLDER:

```
vault/
├── INDEX.md           # Launch page with links to all entities
├── Companies/         # Company profiles
├── Products/          # Product profiles
├── People/            # Key people profiles
├── Technologies/      # Technology overviews
├── Trends/            # Trend analyses
└── Resources/         # Reports, data sources, references
```

## INDEX.md Template

Create the launch page:

```markdown
# [MARKET_TOPIC] Research Vault

> Last updated: {{DATE}}
> Research by: {{AGENT_NAME}}

## Overview
[2-3 sentence summary of the market]

## Quick Navigation

### Companies
- [[Company 1]]
- [[Company 2]]
- ...

### Products & Services
- [[Product 1]]
- [[Product 2]]
- ...

### Key People
- [[Person 1]]
- [[Person 2]]
- ...

### Technologies
- [[Technology 1]]
- ...

### Market Trends
- [[Trend 1]]
- ...

## Market Stats
| Metric | Value | Source |
|--------|-------|--------|
| Market Size | $X | [source] |
| Growth Rate | X% | [source] |
| ... | ... | ... |

## Recent Developments
- [Date]: [Development 1]
- [Date]: [Development 2]

---
*This vault was created using the Maestro Market Research Playbook*
```

## Guidelines

- **Use web search extensively** - Get current market data
- **Be market-specific** - Tailor categories and templates to the actual market
- **Prioritize ruthlessly** - Not all entity types matter equally for every market
- **Set realistic targets** - 5-10 entities per priority category is usually sufficient
- **Create usable templates** - They should capture what matters for this market
