# Structure and Naming

Detailed guidance on skill structure, naming conventions, and writing effective descriptions.

## Table of Contents

- [YAML Frontmatter](#yaml-frontmatter)
- [Naming Conventions](#naming-conventions)
- [Writing Effective Descriptions](#writing-effective-descriptions)
- [File Organization](#file-organization)

## YAML Frontmatter

Every skill must begin with valid YAML frontmatter containing exactly two required fields:

```yaml
---
name: skill-name-here
description: Description of what the skill does and when to use it.
---
```

### Name Field Requirements

**Technical constraints:**
- Maximum 64 characters
- Lowercase letters only (a-z)
- Numbers allowed (0-9)
- Hyphens allowed for word separation (-)
- No other characters permitted

**Validation:**
- ✓ Valid: `processing-pdfs`, `analyzing-data-v2`, `managing-apis`
- ✗ Invalid: `ProcessingPDFs` (uppercase), `processing_pdfs` (underscore), `processing.pdfs` (period)

**Reserved terms:**
- Cannot contain "anthropic" or "claude" anywhere in the name
- These are reserved for official Anthropic skills

### Description Field Requirements

**Technical constraints:**
- Maximum 1024 characters
- Must be non-empty
- Cannot contain XML tags (`<`, `>`)

**Content requirements:**
- Must be a complete, grammatically correct sentence or paragraph
- Should enable skill discovery among potentially 100+ available skills

## Naming Conventions

### Use Gerund Form (verb + -ing)

Gerunds clearly communicate the skill's action:

**Good examples:**
- `processing-pdfs` - "This skill is for processing PDFs"
- `analyzing-spreadsheets` - "This skill is for analyzing spreadsheets"
- `managing-databases` - "This skill is for managing databases"
- `validating-forms` - "This skill is for validating forms"
- `generating-reports` - "This skill is for generating reports"

**Poor examples:**
- `pdf-processor` - Not gerund form
- `spreadsheet-analyzer` - Not gerund form
- `database` - Missing verb entirely
- `forms` - Too vague, no action

### Be Specific and Descriptive

Names should clearly indicate purpose:

**Good examples:**
- `processing-invoices` - Clear: works with invoices
- `analyzing-financial-data` - Clear: financial data analysis
- `managing-customer-records` - Clear: customer record management

**Poor examples:**
- `helper` - What does it help with?
- `utils` - What utilities?
- `processor` - What does it process?
- `manager` - What does it manage?
- `handler` - What does it handle?

### Domain-Specific Naming

When creating domain-specific skills, include the domain:

**Good examples:**
- `processing-medical-claims`
- `analyzing-sales-data`
- `validating-legal-documents`
- `generating-financial-reports`

**Why this works:**
- Immediately clear what domain the skill serves
- Easy to find when working in that domain
- Avoids confusion with generic skills

## Writing Effective Descriptions

The description is critical for skill discovery. Claude uses descriptions to decide which skill to invoke.

### Write in Third Person

Descriptions should describe what the skill does, not what "I" or "you" can do:

**Good:**
```yaml
description: Processes Excel files to extract data, validate formats, and generate summary reports. Use when analyzing spreadsheet data or converting Excel to other formats.
```

**Poor:**
```yaml
description: I can help you work with Excel files and do things with spreadsheets.
```

### Include Usage Triggers

Explicitly state when Claude should use this skill:

**Good examples:**
```yaml
description: Analyzes PDF documents to extract text, tables, and structure. Use when the user provides a PDF file for analysis, data extraction, or content summarization.
```

```yaml
description: Validates form data against business rules and generates error reports. Use when checking user input, processing form submissions, or ensuring data quality before database insertion.
```

```yaml
description: Generates financial reports with charts and summaries from transaction data. Use when the user requests analysis of financial data, budget reports, or expense summaries.
```

### Include Specific Keywords

Think about what words a user might mention that should trigger this skill:

**Example for invoice processing:**
```yaml
description: Processes vendor invoices to extract line items, validate totals, and flag discrepancies. Use when analyzing invoice documents, verifying billing accuracy, AP automation, or preparing payment data.
```

Keywords included: invoices, vendor, line items, totals, billing, AP automation, payment

**Example for API integration:**
```yaml
description: Integrates with REST APIs using proper authentication, error handling, and rate limiting. Use when connecting to external services, making HTTP requests, handling API responses, or debugging API integration issues.
```

Keywords included: REST APIs, authentication, HTTP requests, API responses, external services, integration

### Provide Context About Capabilities

Help Claude understand what the skill can and cannot do:

**Good:**
```yaml
description: Analyzes SQL query performance and suggests optimization strategies. Handles SELECT, JOIN, and subquery optimization. Use when diagnosing slow queries or improving database performance. Does not execute queries directly.
```

**Why this works:**
- States what it does (analyzes, suggests)
- Lists specific capabilities (SELECT, JOIN, subquery)
- Clear usage trigger (slow queries, performance)
- States limitation (doesn't execute)

### Real-World Example Comparison

**Poor description:**
```yaml
name: pdf-tool
description: Works with PDF files
```

Problems:
- Name not in gerund form
- Description too vague
- No usage triggers
- No specific keywords
- Doesn't explain capabilities

**Good description:**
```yaml
name: processing-pdfs
description: Processes PDF documents to extract text, tables, images, and metadata. Handles multi-page analysis, form field extraction, and content summarization. Use when analyzing PDF files, extracting data from forms or reports, or converting PDFs to structured formats like JSON or CSV.
```

Improvements:
- Gerund-form name
- Specific actions (extract, analyze, convert)
- Lists capabilities (text, tables, images, metadata)
- Multiple usage triggers
- Specific keywords (forms, reports, JSON, CSV)

## File Organization

### Single File Skills

Use a single SKILL.md file when:
- Total content is under 400 lines
- All information is closely related
- Users need access to everything immediately
- Content doesn't split naturally into topics

**Structure example:**
```
skill-name/
└── SKILL.md
```

### Multi-File Skills with Progressive Disclosure

Split into multiple files when:
- Approaching 500 lines in SKILL.md
- Content has distinct topics
- Some information is conditional or advanced
- Different audiences need different details

**Structure example:**
```
skill-name/
├── SKILL.md (overview, core workflow)
└── reference/
    ├── topic-one.md
    ├── topic-two.md
    └── advanced-features.md
```

**Critical rule:** All reference files must be ONE level deep. Never create nested references:

❌ **Wrong:**
```
skill-name/
├── SKILL.md
└── reference/
    ├── basics.md
    └── advanced/
        └── details.md  ← Too deep!
```

✓ **Correct:**
```
skill-name/
├── SKILL.md
└── reference/
    ├── basics.md
    └── advanced-details.md  ← One level deep
```

### Reference File Naming

Use descriptive, specific filenames:

**Good:**
- `form_validation_rules.md`
- `api_authentication_patterns.md`
- `data_transformation_examples.md`
- `error_handling_guide.md`

**Poor:**
- `doc1.md`
- `reference.md`
- `stuff.md`
- `misc.md`

### Adding Table of Contents

Include a table of contents in reference files that exceed 100 lines:

```markdown
# Topic Name

Detailed guidance on [topic].

## Table of Contents

- [Section One](#section-one)
- [Section Two](#section-two)
- [Section Three](#section-three)

## Section One

Content here...
```

### Linking Between Files

Reference other files using relative paths:

```markdown
See [Advanced Features](./reference/advanced-features.md) for detailed guidance.

For API authentication, refer to [API Patterns](./reference/api-authentication-patterns.md).
```

## Validation Checklist

Before finalizing structure and naming:

- [ ] YAML frontmatter is valid (name and description fields)
- [ ] Name is ≤64 characters
- [ ] Name uses only lowercase, numbers, and hyphens
- [ ] Name is in gerund form (verb + -ing)
- [ ] Name is specific and descriptive
- [ ] Name doesn't contain "anthropic" or "claude"
- [ ] Description is ≤1024 characters
- [ ] Description has no XML tags
- [ ] Description is written in third person
- [ ] Description includes usage triggers
- [ ] Description includes specific keywords
- [ ] Description explains capabilities clearly
- [ ] SKILL.md is under 500 lines
- [ ] Reference files (if any) are one level deep
- [ ] Reference filenames are descriptive
- [ ] Files >100 lines have table of contents
