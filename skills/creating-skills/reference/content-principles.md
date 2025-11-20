# Content Principles

Core principles for writing effective skill content: conciseness, freedom levels, and progressive disclosure.

## Table of Contents

- [Conciseness](#conciseness)
- [Freedom Levels](#freedom-levels)
- [Progressive Disclosure](#progressive-disclosure)
- [Terminology Consistency](#terminology-consistency)
- [Time-Sensitive Information](#time-sensitive-information)

## Conciseness

Skills share the context window with conversation history and other skills. Every word must earn its place.

### Challenge Every Detail

Before including any information, ask:
- Does Claude already know this?
- Is this essential for the task?
- Could this be inferred from context?
- Does this add unique value?

**Example - Unnecessary detail:**
```markdown
## Processing CSV Files

CSV stands for Comma-Separated Values. It's a file format that stores
tabular data in plain text. Each line represents a row, and commas
separate the values in each column. CSV files can be opened in Excel,
Google Sheets, and other spreadsheet applications.

To process a CSV file:
1. Open the file
2. Read the contents
3. Parse the data
```

**Better - Essential information only:**
```markdown
## Processing CSV Files

When processing CSVs:
1. Validate column headers match expected schema
2. Check for encoding issues (default to UTF-8)
3. Handle missing values explicitly (null vs. empty string)
```

### Focus on Unique Information

Only include what Claude doesn't already know or what's specific to your use case:

**Unnecessary (Claude knows this):**
```markdown
## Using Python Pandas

Pandas is a popular data manipulation library. To use it, first install
with pip, then import it at the top of your script. DataFrames are the
main data structure in pandas.
```

**Better (unique, actionable information):**
```markdown
## Data Processing Requirements

For this workflow, use pandas with these constraints:
- Preserve original column order (df.reindex())
- Handle timestamps in Pacific timezone
- Round currency values to 2 decimal places
```

### Remove Redundant Explanations

Don't explain the same concept multiple times:

**Redundant:**
```markdown
## Step 1: Validate Input
Validate the input data to ensure it meets requirements.

## Step 2: Process Data
After validating the input in step 1, process the validated data.

## Step 3: Generate Output
Using the data that was validated in step 1 and processed in step 2...
```

**Better:**
```markdown
## Workflow

1. **Validate Input**: Check schema and data types
2. **Process Data**: Apply transformations
3. **Generate Output**: Format and export results
```

## Freedom Levels

Match the level of instruction detail to task requirements. Too much freedom causes errors; too little freedom wastes tokens and limits adaptability.

### High Freedom (Text Instructions)

Use when multiple valid approaches exist:

**When to use:**
- Creative or analytical tasks
- Problem-solving with various solutions
- Tasks requiring judgment calls
- Exploratory data analysis

**How to provide:**
- State goals and constraints
- Provide guidelines, not steps
- Allow Claude to choose approach
- Focus on outcomes, not methods

**Example:**
```markdown
## Analyzing User Feedback

Review user feedback to identify:
- Common themes and patterns
- Critical issues requiring immediate attention
- Feature requests with strong user demand
- Sentiment trends over time

Present findings in order of business impact. Include specific quotes
that illustrate each theme.
```

### Medium Freedom (Pseudocode/Guidance)

Use when there are best practices but some flexibility is acceptable:

**When to use:**
- Standard workflows with variations
- Tasks with recommended patterns
- Operations where approach matters but isn't critical

**How to provide:**
- Show recommended approaches
- Explain why certain patterns work
- Allow adaptation to context
- Provide examples of variations

**Example:**
```markdown
## Data Validation Pattern

Recommended validation workflow:

\`\`\`python
# 1. Schema validation first (fast failure)
validate_schema(data)

# 2. Business rules validation
for record in data:
    validate_business_rules(record)
    if invalid:
        collect_errors(record)

# 3. Report all errors together (better UX)
if errors:
    return error_report(errors)
\`\`\`

Adapt this pattern based on:
- Data volume (batch validation for large datasets)
- Error tolerance (fail fast vs. collect all errors)
- Performance requirements (parallel vs. sequential)
```

### Low Freedom (Specific Scripts)

Use when exact sequences are required:

**When to use:**
- API calls with specific requirements
- Database operations with transaction constraints
- File operations with dependencies
- Critical operations where order matters
- Fragile systems requiring exact commands

**How to provide:**
- Exact commands or scripts
- Explicit error handling
- Step-by-step sequences
- Clear dependencies

**Example:**
```markdown
## Database Migration Script

Execute these steps in exact order:

\`\`\`sql
-- 1. Create backup (REQUIRED before proceeding)
CREATE TABLE users_backup AS SELECT * FROM users;

-- 2. Add new column with default value
ALTER TABLE users ADD COLUMN status VARCHAR(20) DEFAULT 'active';

-- 3. Populate based on existing data
UPDATE users
SET status = CASE
    WHEN last_login > NOW() - INTERVAL '30 days' THEN 'active'
    ELSE 'inactive'
END;

-- 4. Add NOT NULL constraint (only after population)
ALTER TABLE users ALTER COLUMN status SET NOT NULL;

-- 5. Verify migration
SELECT status, COUNT(*) FROM users GROUP BY status;
\`\`\`

DO NOT proceed to next step if any step fails.
If errors occur, restore from users_backup table.
```

### Choosing the Right Level

Ask these questions:

1. **What happens if Claude does it differently?**
   - Nothing bad → High freedom
   - Suboptimal but acceptable → Medium freedom
   - Breaks or causes errors → Low freedom

2. **How many correct approaches exist?**
   - Many valid approaches → High freedom
   - Several good patterns → Medium freedom
   - One correct sequence → Low freedom

3. **Is this a creative or technical task?**
   - Creative/analytical → High freedom
   - Standard procedure → Medium freedom
   - Technical operation → Low freedom

## Progressive Disclosure

Keep SKILL.md under 500 lines by splitting content into logical reference files.

### When to Split Content

**Indicators it's time to split:**
- SKILL.md approaching 400-500 lines
- Content has distinct, separable topics
- Some information is rarely needed
- Different user groups need different sections
- Conditional or advanced features exist

**Stay single-file when:**
- Under 400 lines total
- All content is tightly integrated
- Users need everything simultaneously
- Splitting would harm usability

### Pattern 1: Topic-Based References

Split by functional area:

```
skill-name/
├── SKILL.md (overview + core workflow)
└── reference/
    ├── validation-rules.md
    ├── transformation-patterns.md
    ├── output-formats.md
    └── error-handling.md
```

**SKILL.md structure:**
```markdown
# Skill Overview

Core workflow here...

## Validation
For detailed validation rules, see [Validation Rules](./reference/validation-rules.md)

## Transformations
For transformation patterns, see [Transformations](./reference/transformation-patterns.md)
```

### Pattern 2: Domain-Based References

Split by domain or category:

```
skill-name/
├── SKILL.md (general workflow)
└── reference/
    ├── finance-operations.md
    ├── sales-operations.md
    └── operations-management.md
```

**When to use:**
- Skill serves multiple domains
- Each domain has unique requirements
- Users typically work in one domain at a time

### Pattern 3: Conditional Details

Split by usage frequency:

```
skill-name/
├── SKILL.md (common operations)
└── reference/
    ├── advanced-features.md
    ├── troubleshooting.md
    └── migration-from-v1.md
```

**When to use:**
- Most users need only basic features
- Advanced features are rarely used
- Troubleshooting info clutters main workflow
- Legacy information needs to be accessible

### Linking Strategy

**From SKILL.md to reference files:**
```markdown
For detailed validation rules, see [Validation Rules](./reference/validation-rules.md)
```

**Inline references for essential details:**
```markdown
## Data Validation

Basic validation checks (required):
- Non-empty fields
- Correct data types
- Valid ranges

For complete validation rules including business logic, see [Validation Rules](./reference/validation-rules.md)
```

**When to inline vs. reference:**
- **Inline**: Information needed for 80%+ of use cases
- **Reference**: Details needed occasionally or conditionally

### Critical Rule: One Level Deep Only

**Never nest references within references:**

❌ **Wrong approach:**

```
SKILL.md references → reference/basics.md references → reference/advanced/details.md
```

This breaks because Claude may not read the deeply nested file.

✓ **Correct approach:**

```
SKILL.md references → reference/basics.md (complete, no further references)
SKILL.md references → reference/advanced-details.md (complete, no further references)
```

**Why this matters:**
- Claude needs to explicitly read each file
- Deep nesting means information may be missed
- One level deep ensures complete information access

## Terminology Consistency

Choose one term for each concept and use it throughout all files.

### Common Consistency Issues

**API terminology:**
❌ Inconsistent:
- "API endpoint"
- "service URL"
- "API path"
- "endpoint address"
- "service endpoint"

✓ Consistent: Always use "API endpoint"

**Data terminology:**
❌ Inconsistent:
- "dataset"
- "data set"
- "data file"
- "data source"

✓ Consistent: Choose one term based on context:
- Use "dataset" for analytical data
- Use "data file" for file operations
- Use "data source" for origin systems

### Creating a Terminology Guide

For complex skills, maintain consistency by defining terms upfront:

```markdown
## Terminology

Throughout this skill:
- **Record**: A single row of data (database context)
- **Document**: A single item to process (file context)
- **Validation**: Checking data meets schema requirements
- **Verification**: Checking data meets business rules
```

### Validation Technique

Before finalizing, search for related terms:
1. List all terms for key concepts
2. Search skill files for each variation
3. Replace with chosen standard term
4. Document terminology decisions

## Time-Sensitive Information

Avoid time-based references that will become outdated.

### Avoid Date-Based Conditionals

❌ **Poor approach:**
```markdown
## Before January 2024
Use the v1 API with this format:
\`\`\`python
api.call(format='old')
\`\`\`

## After January 2024
Use the v2 API with this format:
\`\`\`python
api.call(format='new')
\`\`\`
```

✓ **Better approach:**
```markdown
## Current API (v2)
Use this format for all new implementations:
\`\`\`python
api.call(format='new')
\`\`\`

## Legacy API (v1) - Deprecated
If working with existing code, you may encounter:
\`\`\`python
api.call(format='old')
\`\`\`
Consider migrating to v2 API when possible.
```

### Use Version-Based References

Instead of dates, use versions or feature states:

**Good patterns:**
- "Current version" vs. "Legacy version"
- "Recommended approach" vs. "Old approach"
- "Standard method" vs. "Deprecated method"
- "v2 API" vs. "v1 API"

### Document Evolution

When features change:

```markdown
## Current Implementation (Recommended)

Use this approach for all new work:
[current instructions]

## Previous Implementation

Older code may use this pattern:
[old instructions]

This approach is deprecated because [reason].
```

## Validation Checklist

Content principles checklist:

- [ ] Every piece of information is essential
- [ ] No redundant explanations
- [ ] Focus on unique information Claude needs
- [ ] Appropriate freedom level for each task type
- [ ] SKILL.md under 500 lines
- [ ] Reference files (if any) are one level deep only
- [ ] Consistent terminology throughout all files
- [ ] No time-sensitive or date-based information
- [ ] Clear linking between SKILL.md and references
- [ ] Table of contents in files >100 lines
