# Initialize Research Vault

## Context

- **Playbook:** Market Research
- **Agent:** {{AGENT_NAME}}
- **Auto Run Folder:** {{AUTORUN_FOLDER}}
- **Loop:** {{LOOP_NUMBER}}

## Purpose

This document initializes the research vault with the proper folder structure, creates entity-specific agents, and sets up Claude Code integration via `.claude/` with symlinks. **This runs once at the start before the main research loop begins.**

## Instructions

1. **Read the Agent-Prompt.md** to get the configured MARKET_TOPIC and OUTPUT_FOLDER
2. **Create the vault folder structure** with visible Agents/ and Commands/ folders
3. **Set up .claude/ with symlinks** so Claude Code can discover agents and commands
4. **Generate entity-type agents** customized for the market topic
5. **Create the research command** for easy invocation
6. **Initialize INDEX.md** as the vault launch page

## Initialization Checklist

- [ ] **Initialize vault structure**: Read Agent-Prompt.md for MARKET_TOPIC and OUTPUT_FOLDER. Create the vault folder structure including Agents/, Commands/, .claude/ with symlinks, and entity subfolders. Generate entity-specific agents based on the market. Create initial INDEX.md and CLAUDE.md for the vault.

## Vault Structure to Create

```
[OUTPUT_FOLDER]/
├── .claude/
│   ├── agents -> ../Agents      # Symlink to Agents folder
│   └── commands -> ../Commands  # Symlink to Commands folder
├── Agents/                      # Visible in Obsidian, holds agent definitions
│   ├── company-researcher.md
│   ├── product-researcher.md
│   ├── person-researcher.md
│   ├── technology-researcher.md
│   └── trend-researcher.md
├── Commands/                    # Visible in Obsidian, holds slash commands
│   └── research.md
├── Companies/                   # Company profiles
├── Products/                    # Product profiles
├── People/                      # Key people profiles
├── Technologies/                # Technology overviews
├── Trends/                      # Trend analyses
├── Resources/                   # Reports, data sources
├── INDEX.md                     # Launch page
└── CLAUDE.md                    # Vault-specific Claude instructions
```

## Create .claude/ with Symlinks

```bash
# Create .claude directory
mkdir -p [OUTPUT_FOLDER]/.claude

# Create symlinks (relative paths so they work if vault is moved)
cd [OUTPUT_FOLDER]/.claude
ln -s ../Agents agents
ln -s ../Commands commands
```

## Entity Agent Template

Create agents in `[OUTPUT_FOLDER]/Agents/` for each entity type:

### company-researcher.md

```markdown
---
name: company-researcher
description: Researches a company and creates a structured profile for the [MARKET_TOPIC] knowledge vault.
model: inherit
---

# Company Researcher Agent

**Purpose:** Research a specific company in the [MARKET_TOPIC] market and create a comprehensive profile.

## Input
- Company name to research
- Any known information (website, description, etc.)

## Process
1. **Web Search** - Gather information about the company:
   - Official website and about page
   - Crunchbase/LinkedIn profiles
   - Recent news and press releases
   - Funding announcements
   - Product information

2. **Create Profile** - Write markdown file in `Companies/[Company-Name].md`:
   - Use the company template from market analysis
   - Include all discoverable facts with sources
   - Add [[wiki-links]] to related entities
   - Note any information gaps

3. **Update INDEX.md** - Add link to new company under Companies section

## Output
- Company markdown file in `Companies/`
- Updated INDEX.md
- Research notes in log file

## Quality Standards
- Cross-reference facts from multiple sources
- Include source URLs for all claims
- Note when information is uncertain
- Prioritize recent information (last 2 years)
```

### product-researcher.md

```markdown
---
name: product-researcher
description: Researches a product/service and creates a structured profile for the [MARKET_TOPIC] knowledge vault.
model: inherit
---

# Product Researcher Agent

**Purpose:** Research a specific product/service in the [MARKET_TOPIC] market and create a comprehensive profile.

## Input
- Product name to research
- Company that makes it (if known)

## Process
1. **Web Search** - Gather information about the product:
   - Official product page
   - Feature lists and documentation
   - Pricing information
   - Reviews and comparisons (G2, Capterra, etc.)
   - Case studies and customer testimonials

2. **Create Profile** - Write markdown file in `Products/[Product-Name].md`:
   - Use the product template from market analysis
   - Include features, pricing, target customers
   - Add [[wiki-links]] to company and competitors
   - Note any information gaps

3. **Update INDEX.md** - Add link to new product under Products section

## Output
- Product markdown file in `Products/`
- Updated INDEX.md
- Research notes in log file
```

### person-researcher.md

```markdown
---
name: person-researcher
description: Researches a key person and creates a structured profile for the [MARKET_TOPIC] knowledge vault.
model: inherit
---

# Person Researcher Agent

**Purpose:** Research a key person in the [MARKET_TOPIC] market and create a comprehensive profile.

## Input
- Person's name
- Known role/company (if available)

## Process
1. **Web Search** - Gather information about the person:
   - LinkedIn profile
   - Company bio page
   - Conference talks and interviews
   - Published articles or thought leadership
   - Career history

2. **Create Profile** - Write markdown file in `People/[Person-Name].md`:
   - Use the person template from market analysis
   - Include career history, achievements, thought leadership
   - Add [[wiki-links]] to companies and other people
   - Note any information gaps

3. **Update INDEX.md** - Add link to new person under People section

## Output
- Person markdown file in `People/`
- Updated INDEX.md
- Research notes in log file
```

### technology-researcher.md

```markdown
---
name: technology-researcher
description: Researches a technology/standard and creates a structured profile for the [MARKET_TOPIC] knowledge vault.
model: inherit
---

# Technology Researcher Agent

**Purpose:** Research a specific technology, platform, or standard in the [MARKET_TOPIC] market.

## Input
- Technology name
- Category (protocol, platform, framework, etc.)

## Process
1. **Web Search** - Gather information about the technology:
   - Official documentation
   - Technical specifications
   - Adoption statistics
   - Companies using it
   - Comparison with alternatives

2. **Create Profile** - Write markdown file in `Technologies/[Technology-Name].md`:
   - Use the technology template from market analysis
   - Include technical details, use cases, adoption
   - Add [[wiki-links]] to companies and products using it
   - Note any information gaps

3. **Update INDEX.md** - Add link under Technologies section

## Output
- Technology markdown file in `Technologies/`
- Updated INDEX.md
- Research notes in log file
```

### trend-researcher.md

```markdown
---
name: trend-researcher
description: Researches a market trend and creates a structured analysis for the [MARKET_TOPIC] knowledge vault.
model: inherit
---

# Trend Researcher Agent

**Purpose:** Research and analyze a specific trend in the [MARKET_TOPIC] market.

## Input
- Trend name/description
- Timeframe to focus on

## Process
1. **Web Search** - Gather information about the trend:
   - Industry reports and analyses
   - News coverage
   - Expert commentary
   - Data and statistics
   - Companies driving or affected by trend

2. **Create Profile** - Write markdown file in `Trends/[Trend-Name].md`:
   - Use the trend template from market analysis
   - Include drivers, implications, timeline
   - Add [[wiki-links]] to related companies and technologies
   - Include quantitative data where available

3. **Update INDEX.md** - Add link under Trends section

## Output
- Trend markdown file in `Trends/`
- Updated INDEX.md
- Research notes in log file
```

## Research Command Template

Create in `[OUTPUT_FOLDER]/Commands/research.md`:

```markdown
# Research Entity

Research a specific entity and add it to the [MARKET_TOPIC] knowledge vault.

## Usage
Provide the entity type and name to research.

## Process
1. Identify the appropriate agent for the entity type:
   - Company → use company-researcher agent
   - Product → use product-researcher agent
   - Person → use person-researcher agent
   - Technology → use technology-researcher agent
   - Trend → use trend-researcher agent

2. Spawn the agent with the Task tool to research the entity

3. Verify the profile was created and INDEX.md was updated

## Example
"Research the company Acme Corp"
→ Spawns company-researcher agent
→ Creates Companies/Acme-Corp.md
→ Updates INDEX.md
```

## Vault CLAUDE.md Template

Create in `[OUTPUT_FOLDER]/CLAUDE.md`:

```markdown
# [MARKET_TOPIC] Research Vault

## Purpose

This vault contains structured research about the [MARKET_TOPIC] market, organized as interlinked markdown files compatible with Obsidian.

## Structure

- **Companies/** - Company profiles
- **Products/** - Product/service profiles
- **People/** - Key people in the market
- **Technologies/** - Technologies and platforms
- **Trends/** - Market trends and analyses
- **Resources/** - Reports, data sources, references
- **Agents/** - Research agents for each entity type
- **Commands/** - Slash commands for common operations

## Agents

Located in `Agents/` (also accessible via `.claude/agents`):

| Agent | Purpose |
|-------|---------|
| company-researcher | Research and profile companies |
| product-researcher | Research and profile products |
| person-researcher | Research and profile key people |
| technology-researcher | Research and profile technologies |
| trend-researcher | Research and analyze market trends |

## Commands

Located in `Commands/` (also accessible via `.claude/commands`):

| Command | Purpose |
|---------|---------|
| /research | Research a specific entity |

## Conventions

- Use `[[Entity Name]]` for inter-page links
- Keep profiles factual with source citations
- Update INDEX.md when adding new entities
- Use consistent naming: `Entity-Name.md` (kebab-case)

## Working in This Vault

1. Use the research agents to add new entities
2. Manually edit profiles to add context or corrections
3. Check INDEX.md for navigation
4. Use the graph view in Obsidian to explore connections
```

## INDEX.md Template

Create initial launch page in `[OUTPUT_FOLDER]/INDEX.md`:

```markdown
# [MARKET_TOPIC] Research Vault

> Research initiated: {{DATE}}
> Agent: {{AGENT_NAME}}

## Overview

This vault contains structured research about the **[MARKET_TOPIC]** market.

## Quick Navigation

### Companies
_No companies researched yet._

### Products & Services
_No products researched yet._

### Key People
_No people researched yet._

### Technologies
_No technologies researched yet._

### Market Trends
_No trends researched yet._

## Research Tools

### Agents
- [[Agents/company-researcher|Company Researcher]]
- [[Agents/product-researcher|Product Researcher]]
- [[Agents/person-researcher|Person Researcher]]
- [[Agents/technology-researcher|Technology Researcher]]
- [[Agents/trend-researcher|Trend Researcher]]

### Commands
- `/research` - Research a specific entity

## Statistics

| Category | Count |
|----------|-------|
| Companies | 0 |
| Products | 0 |
| People | 0 |
| Technologies | 0 |
| Trends | 0 |
| **Total Entities** | 0 |

---
*This vault was initialized by the Maestro Market Research Playbook*
```

## How to Know You're Done

This task is complete when:

1. All folders exist (Companies/, Products/, People/, Technologies/, Trends/, Resources/, Agents/, Commands/)
2. `.claude/` folder exists with working symlinks to Agents/ and Commands/
3. All five entity-type agents are created in Agents/
4. The research command is created in Commands/
5. INDEX.md exists with the market topic
6. CLAUDE.md exists with vault documentation

## Notes

- The symlinks allow Claude Code to discover agents/commands via `.claude/`
- Agents/ and Commands/ folders are visible in Obsidian for easy reference
- Entity agents are customized with the MARKET_TOPIC from configuration
- This only runs once - subsequent loops skip to 1_ANALYZE.md
