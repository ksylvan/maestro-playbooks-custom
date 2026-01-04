# Entity Evaluation - Prioritize Research Targets

## Context

- **Playbook:** Market Research
- **Agent:** {{AGENT_NAME}}
- **Auto Run Folder:** {{AUTORUN_FOLDER}}
- **Loop:** {{LOOP_NUMBER}}

## Objective

Evaluate discovered entities and prioritize them for research. Assess each entity's importance to understanding the market and the effort required to research it thoroughly.

## Instructions

1. **Read discovered entities** from `{{AUTORUN_FOLDER}}/LOOP_{{LOOP_NUMBER}}_ENTITIES.md`
2. **Read the research plan** from `{{AUTORUN_FOLDER}}/LOOP_{{LOOP_NUMBER}}_PLAN.md` (if it exists)
3. **Select ONE unevaluated entity** (status = PENDING, not yet in plan)
4. **Evaluate importance and research effort**
5. **Add to research plan** with priority rating

## Evaluation Checklist

- [ ] **Evaluate one entity (or skip if empty)**: Read `{{AUTORUN_FOLDER}}/LOOP_{{LOOP_NUMBER}}_ENTITIES.md` to find PENDING entities. If the file contains no entities OR all entities have already been evaluated in `{{AUTORUN_FOLDER}}/LOOP_{{LOOP_NUMBER}}_PLAN.md`, mark this task complete without changes. Otherwise, pick ONE entity that hasn't been evaluated yet. Assess its importance and research effort. Append evaluation to `{{AUTORUN_FOLDER}}/LOOP_{{LOOP_NUMBER}}_PLAN.md` with appropriate status.

## Importance Criteria

| Level | Description |
|-------|-------------|
| **CRITICAL** | Market leader, defines the space, must-know entity |
| **HIGH** | Significant player, important for market understanding |
| **MEDIUM** | Notable entity, adds depth to research |
| **LOW** | Minor player, nice-to-have but not essential |

### Importance Factors

- Market share or influence
- Innovation leadership
- Funding/growth trajectory
- Media/analyst attention
- Interconnectedness with other entities
- Relevance to understanding market dynamics

## Research Effort Criteria

| Level | Description |
|-------|-------------|
| **EASY** | Public company, well-documented, abundant information |
| **MEDIUM** | Good coverage but requires synthesis from multiple sources |
| **HARD** | Private company, limited coverage, requires deep digging |
| **VERY HARD** | Stealth mode, minimal public info, may not be researchable |

### Effort Factors

- Availability of public information
- Quality of company website/documentation
- Press coverage and analyst reports
- Executive visibility and interviews
- Complexity of the entity (simple product vs. diversified company)

## Output Format

Append to `{{AUTORUN_FOLDER}}/LOOP_{{LOOP_NUMBER}}_PLAN.md`:

```markdown
---

## [Entity Name] - Evaluated [YYYY-MM-DD]

**Source:** [Reference to entry in ENTITIES.md]
**Type:** [Company | Product | Person | Technology | Trend | Investor]
**Category:** [Which category from market analysis]

### Quick Profile
[2-3 sentences about what this entity is and does]

### Importance Assessment
- **Rating:** [CRITICAL | HIGH | MEDIUM | LOW]
- **Justification:** [Why this rating]
- **Key Questions to Answer:**
  1. [Question 1 this research should answer]
  2. [Question 2]
  3. [Question 3]

### Research Effort Assessment
- **Rating:** [EASY | MEDIUM | HARD | VERY HARD]
- **Justification:** [Why this rating]
- **Primary Sources Available:**
  - [Source 1 - e.g., company website]
  - [Source 2 - e.g., Crunchbase profile]
  - [Source 3 - e.g., recent news articles]

### Expected Connections
Entities this will likely link to:
- [[Entity A]] - [relationship]
- [[Entity B]] - [relationship]

### Status: [PENDING | SKIP - reason]
```

## Status Decision Matrix

| Importance | Effort | Status |
|------------|--------|--------|
| CRITICAL | Any | `PENDING` - Must research |
| HIGH | EASY/MEDIUM | `PENDING` - High value, reasonable effort |
| HIGH | HARD | `PENDING` - Worth the effort |
| HIGH | VERY HARD | `PENDING - MANUAL REVIEW` - May need human help |
| MEDIUM | EASY | `PENDING` - Quick win |
| MEDIUM | MEDIUM | `PENDING` - Good value |
| MEDIUM | HARD/VERY HARD | `SKIP - Effort exceeds value` |
| LOW | EASY | `PENDING` - If time permits |
| LOW | MEDIUM+ | `SKIP - Low priority` |

## Guidelines

- **One entity per run** - Thorough evaluation beats rushed assessments
- **Be honest about effort** - Don't underestimate research difficulty
- **Think about connections** - High-connectivity entities add more value
- **Consider the portfolio** - Balance entity types for comprehensive coverage
- **Note key questions** - Guides the research phase
- **Identify sources upfront** - Saves time during research

## How to Know You're Done

This task is complete when ONE of the following is true:

**Option A - Evaluated an entity:**

1. You've evaluated exactly ONE entity from `{{AUTORUN_FOLDER}}/LOOP_{{LOOP_NUMBER}}_ENTITIES.md`
2. You've appended a complete evaluation to `{{AUTORUN_FOLDER}}/LOOP_{{LOOP_NUMBER}}_PLAN.md`
3. The evaluation includes both importance and research effort ratings
4. The status is set according to the decision matrix above

**Option B - No entities to evaluate:**

1. `LOOP_{{LOOP_NUMBER}}_ENTITIES.md` contains no entities, OR
2. All entities have already been evaluated in `LOOP_{{LOOP_NUMBER}}_PLAN.md`
3. Mark this task complete without making changes

This graceful handling of empty states prevents the pipeline from stalling when discovery yields no entities.
