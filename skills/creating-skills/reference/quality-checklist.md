# Quality Checklist

Complete validation checklist for skill quality and common mistakes to avoid.

## Table of Contents

- [Complete Quality Checklist](#complete-quality-checklist)
- [Common Mistakes](#common-mistakes)
- [Iterative Development Process](#iterative-development-process)
- [Testing Strategies](#testing-strategies)
- [Cross-Model Compatibility](#cross-model-compatibility)

## Complete Quality Checklist

Use this comprehensive checklist before finalizing any skill.

### Structure and Naming

- [ ] **Valid YAML frontmatter** with `name` and `description` fields
- [ ] **Name is ≤64 characters**
- [ ] **Name uses only lowercase letters, numbers, and hyphens**
- [ ] **Name is in gerund form** (verb + -ing): `processing-pdfs`, not `pdf-processor`
- [ ] **Name is specific and descriptive**, not vague (`helper`, `utils`, `manager`)
- [ ] **Name doesn't contain** "anthropic" or "claude" (reserved words)
- [ ] **Description is ≤1024 characters**
- [ ] **Description contains no XML tags** (`<`, `>`)
- [ ] **Description is written in third person**, not first person ("I can help...")
- [ ] **Description includes usage triggers** (when Claude should use this skill)
- [ ] **Description includes specific keywords** for discovery
- [ ] **Description explains capabilities clearly**

### File Organization

- [ ] **SKILL.md is under 500 lines**
- [ ] **Reference files (if any) are one level deep only** (no nested references)
- [ ] **Reference filenames are descriptive**: `validation_rules.md`, not `doc2.md`
- [ ] **Files >100 lines have table of contents** at the top
- [ ] **Links to reference files use relative paths**: `./reference/file.md`
- [ ] **All reference files are actually referenced** from SKILL.md

### Content Quality

- [ ] **Only includes information Claude doesn't already have**
- [ ] **No redundant explanations** or repeated information
- [ ] **Focuses on unique, actionable information**
- [ ] **Appropriate freedom level** for each task type:
  - High freedom for creative/analytical tasks
  - Medium freedom for standard workflows
  - Low freedom for critical/exact operations
- [ ] **Consistent terminology throughout** all files
- [ ] **No time-sensitive information** (avoid "Before January 2024...")
- [ ] **Concrete examples provided** where helpful
- [ ] **Workflows use checkboxes** for complex multi-step tasks
- [ ] **Templates provided** for format-critical outputs
- [ ] **Feedback loops implemented** for validation-heavy operations

### Technical Implementation

- [ ] **Scripts have explicit error handling** with specific messages
- [ ] **Error messages include context and solutions**
- [ ] **File paths use forward slashes** for cross-platform compatibility
- [ ] **Dependencies documented** with versions and purposes
- [ ] **Configuration values explained** (no "magic numbers")
- [ ] **MCP tools use fully qualified names**: `ServerName:tool_name`
- [ ] **Clear indication** whether to execute or reference scripts
- [ ] **Validation scripts** provide comprehensive error reports

### Testing and Validation

- [ ] **Skill works for intended use cases**
- [ ] **Instructions are clear and actionable**
- [ ] **Examples demonstrate expected behavior**
- [ ] **Tested with realistic scenarios**, not just artificial ones
- [ ] **Error handling tested** with invalid inputs
- [ ] **Cross-model compatibility considered** (Haiku, Sonnet, Opus)

### Documentation Quality

- [ ] **Purpose is immediately clear** from first paragraph
- [ ] **Quick start workflow provided** for common cases
- [ ] **Advanced features documented** but not overwhelming
- [ ] **Troubleshooting guidance included** for common issues
- [ ] **Links work** and point to correct files
- [ ] **Code examples are syntactically correct**
- [ ] **Formatting is consistent** throughout

## Common Mistakes

Learn from these frequently encountered mistakes when creating skills.

### Mistake 1: Vague Skill Names

❌ **Bad:**
- `helper`
- `utils`
- `processor`
- `manager`
- `handler`

✓ **Good:**
- `processing-invoices`
- `analyzing-financial-data`
- `validating-forms`
- `generating-reports`

**Why it matters:** Vague names make skills impossible to find and choose among many skills.

### Mistake 2: Poor Descriptions

❌ **Bad:**
```yaml
description: Helps with PDF files and documents
```

**Problems:**
- Too vague
- No usage triggers
- No specific keywords
- Doesn't explain what "helps" means

✓ **Good:**
```yaml
description: Processes PDF documents to extract text, tables, images, and metadata. Handles form field extraction and multi-page analysis. Use when analyzing PDF files, extracting data from forms or reports, or converting PDFs to structured formats like JSON or CSV.
```

**Why better:**
- Specific actions (extract, analyze, convert)
- Lists capabilities
- Multiple usage triggers
- Specific keywords (forms, reports, JSON, CSV)

### Mistake 3: Too Much Freedom for Critical Operations

❌ **Bad:**
```markdown
## Database Migration

Migrate the user table to the new schema. Make sure to back up first
and handle errors appropriately.
```

**Problems:**
- Critical operation with vague instructions
- No specific sequence
- "Appropriately" is undefined

✓ **Good:**
```markdown
## Database Migration

Execute these steps in EXACT order:

1. Create backup: `CREATE TABLE users_backup AS SELECT * FROM users;`
2. Add column: `ALTER TABLE users ADD COLUMN status VARCHAR(20) DEFAULT 'active';`
3. Populate data: [specific UPDATE query]
4. Add constraint: `ALTER TABLE users ALTER COLUMN status SET NOT NULL;`
5. Verify: `SELECT COUNT(*) FROM users WHERE status IS NULL;`

DO NOT proceed to next step if any step fails.
If errors occur, restore from users_backup.
```

**Why better:**
- Exact commands provided
- Explicit ordering
- Clear error handling
- Verification step

### Mistake 4: Too Little Freedom for Creative Tasks

❌ **Bad:**
```markdown
## Analyzing User Feedback

Step 1: Read all feedback entries
Step 2: Count mentions of each feature
Step 3: Calculate percentages
Step 4: Sort by frequency
Step 5: List top 10 features
Step 6: Write summary in this format: [exact format]
```

**Problems:**
- Over-specified for analytical task
- Doesn't allow for insight
- Misses patterns that don't fit the template

✓ **Good:**
```markdown
## Analyzing User Feedback

Review feedback to identify:
- Common themes and patterns
- Critical issues requiring immediate attention
- Feature requests with strong user demand
- Sentiment trends over time

Present findings in order of business impact. Include specific quotes
that illustrate each theme. Adapt analysis depth to the amount and
complexity of feedback.
```

**Why better:**
- States goals, not steps
- Allows Claude to find patterns
- Encourages insights
- Adapts to context

### Mistake 5: Deep Nesting of Reference Files

❌ **Bad:**
```
skill-name/
├── SKILL.md
└── reference/
    ├── basics.md
    └── advanced/
        ├── details.md ← Too deep!
        └── examples/
            └── code.md ← Way too deep!
```

**Problems:**
- Claude may not read deeply nested files
- Files might be missed
- Harder to maintain

✓ **Good:**
```
skill-name/
├── SKILL.md
└── reference/
    ├── basics.md
    ├── advanced-details.md
    └── advanced-examples.md
```

**Why better:**
- All files one level deep
- Guaranteed to be read
- Easier to find and maintain

### Mistake 6: Windows-Style File Paths

❌ **Bad:**
```python
data_file = "data\\input\\customers.csv"
output_dir = "reports\\2024\\march"
config = "C:\\Users\\name\\config.json"
```

**Problems:**
- Fails on Unix-like systems (Linux, macOS)
- Not portable
- Causes errors

✓ **Good:**
```python
data_file = "data/input/customers.csv"
output_dir = "reports/2024/march"
# For absolute paths, use pathlib
from pathlib import Path
config = Path.home() / "config.json"
```

**Why better:**
- Works on all platforms
- Portable code
- No surprises

### Mistake 7: Assumed Context

❌ **Bad:**
```markdown
## Processing the Files

Use the standard format and apply the usual transformations.
Make sure to follow the guidelines.
```

**Problems:**
- What is "standard format"?
- What are "usual transformations"?
- What guidelines?

✓ **Good:**
```markdown
## Processing Invoice Files

Convert invoice CSV files to JSON format:

Input format (CSV):
- Columns: invoice_id, date, amount, customer_id
- Date format: YYYY-MM-DD
- Amount: decimal with 2 places

Transformations:
1. Convert date to ISO 8601 timestamp
2. Convert amount to cents (integer)
3. Add generated UUID field
4. Validate customer_id exists in customer database

Output format (JSON):
\`\`\`json
{
  "id": "generated-uuid",
  "invoiceId": "...",
  "timestamp": "2024-03-15T00:00:00Z",
  "amountCents": 12345,
  "customerId": "..."
}
\`\`\`
```

**Why better:**
- Explicit formats
- Specific transformations
- No assumptions
- Clear examples

### Mistake 8: Missing Examples

❌ **Bad:**
```markdown
## API Request Format

Make a POST request to the endpoint with the required fields in JSON format.
Include authentication headers.
```

**Problems:**
- What are "required fields"?
- What authentication format?
- No concrete example

✓ **Good:**
```markdown
## API Request Format

\`\`\`python
import requests

response = requests.post(
    url="https://api.example.com/v1/users",
    headers={
        "Authorization": "Bearer YOUR_API_KEY",
        "Content-Type": "application/json"
    },
    json={
        "name": "John Doe",
        "email": "john@example.com",
        "role": "user"
    }
)
\`\`\`

Required fields:
- `name` (string): User's full name
- `email` (string): Valid email address
- `role` (string): One of: "user", "admin", "viewer"
```

**Why better:**
- Concrete, runnable example
- Shows exact format
- Lists requirements explicitly
- Includes authentication

### Mistake 9: No Validation or Verification

❌ **Bad:**
```markdown
## Data Import

1. Read the CSV file
2. Import to database
3. Done
```

**Problems:**
- No validation
- No error handling
- No way to verify success

✓ **Good:**
```markdown
## Data Import with Validation

1. **Validate Input**
   - [ ] File exists and is readable
   - [ ] CSV has required columns
   - [ ] No malformed rows

2. **Import with Verification**
   - [ ] Import data to staging table
   - [ ] Verify row count matches source
   - [ ] Check for constraint violations
   - [ ] Run data quality checks

3. **Promote to Production**
   - [ ] If all checks pass, copy to production table
   - [ ] Verify final row count
   - [ ] Update import log

4. **Cleanup**
   - [ ] Archive source file
   - [ ] Clear staging table

If any step fails, stop and report specific errors.
```

**Why better:**
- Validation at each step
- Verification of results
- Clear success criteria
- Error handling

### Mistake 10: Bloated Content

❌ **Bad:**
```markdown
## Working with CSV Files

CSV stands for Comma-Separated Values. It's a simple file format used
to store tabular data, such as a spreadsheet or database. CSV files
can be opened in Excel, Google Sheets, LibreOffice Calc, and many
other programs. The format has been around since the early days of
computing. Each line in a CSV file represents a row, and commas
separate the values in each column. However, different programs may
use different delimiters like semicolons or tabs...

[500 more lines explaining CSV basics that Claude already knows]
```

**Problems:**
- Includes information Claude already knows
- Wastes context window
- Buries unique information

✓ **Good:**
```markdown
## CSV Processing Requirements

For this workflow, handle CSV files with these specific requirements:

- Encoding: Always UTF-8 (reject other encodings)
- Delimiter: Comma only (fail if semicolon or tab detected)
- Headers: Must be present in row 1
- Required columns: id, name, email, created_date
- Date format: YYYY-MM-DD (fail if other format)
- Max file size: 100MB (reject larger files)

Validation script: [specific validation code]
```

**Why better:**
- Only unique information
- Specific requirements
- Actionable constraints
- Focuses on what matters

## Iterative Development Process

Build effective skills through iteration, not perfection on first try.

### Step 1: Build Evaluations First

Before writing extensive documentation:

**Create test scenarios:**
```markdown
## Test Scenarios for Invoice Processing Skill

1. **Basic invoice processing**
   - Input: Standard invoice CSV with 10 rows
   - Expected: JSON output with all fields correctly transformed
   - Success criteria: All 10 rows processed, no errors

2. **Handling missing data**
   - Input: Invoice CSV with 3 missing dates, 2 missing amounts
   - Expected: Error report identifying specific issues
   - Success criteria: Specific rows and fields identified

3. **Large file processing**
   - Input: Invoice CSV with 50,000 rows
   - Expected: Batch processing with progress updates
   - Success criteria: Completes without memory issues, <5min runtime
```

**Why test scenarios first:**
- Clarifies what "success" looks like
- Identifies edge cases early
- Prevents over-engineering
- Focuses on real requirements

### Step 2: Initial Development

Start minimal:

**First version:**
```markdown
---
name: processing-invoices
description: Processes invoice CSV files to JSON format with validation.
---

# Invoice Processing

## Quick Workflow

1. Validate CSV format
2. Transform to JSON
3. Report results

[Basic transformation script]
```

**Don't include yet:**
- Advanced features
- Edge case handling
- Optimization tips
- Alternative approaches

**Add only when needed based on real usage.**

### Step 3: Real-World Testing

Test with actual usage:

**Testing approach:**
1. Use fresh Claude instance (clear conversation)
2. Give real tasks, not artificial examples
3. Don't guide or hint
4. Observe where Claude struggles or succeeds
5. Note specific problems

**What to observe:**
- Does Claude choose this skill when appropriate?
- Does Claude follow the instructions correctly?
- What causes confusion or errors?
- What information is missing?
- What information is never used?

### Step 4: Iterate Based on Behavior

Update based on observed problems:

**If Claude didn't use the skill when it should:**
→ Improve description with better triggers and keywords

**If Claude misunderstood instructions:**
→ Add examples or clarify ambiguous language

**If Claude made specific errors:**
→ Add guidance for those cases

**If Claude asked for information:**
→ Add that information to the skill

**If parts were never used:**
→ Remove redundant content

### Development Cycle

```
Create test scenarios
       ↓
Build minimal skill
       ↓
Test with fresh Claude instance
       ↓
Observe behavior
       ↓
Identify problems
       ↓
Update skill
       ↓
Repeat until effective
```

**Don't assume - test and verify.**

## Testing Strategies

Practical approaches for testing skills before finalizing.

### Test Scenario Template

```markdown
## Test: [Scenario Name]

**Goal:** [What this test validates]

**Setup:**
- [Required files or data]
- [Environment requirements]
- [Prerequisites]

**Steps:**
1. [Action step 1]
2. [Action step 2]
3. [Action step 3]

**Expected Behavior:**
- [What should happen]
- [Expected outputs]
- [Success criteria]

**Common Failures:**
- [What might go wrong]
- [How to identify]

**Pass/Fail Criteria:**
- [ ] [Specific criterion 1]
- [ ] [Specific criterion 2]
- [ ] [Specific criterion 3]
```

### Example Test Scenarios

**Test 1: Basic Happy Path**
```markdown
## Test: Standard Invoice Processing

**Goal:** Verify skill handles standard invoice correctly

**Setup:**
- File: `test_invoices.csv` with 10 valid rows
- Columns: invoice_id, date, amount, customer_id
- All data valid

**Steps:**
1. Ask Claude to "Process the invoices in test_invoices.csv"
2. Observe if skill is automatically selected
3. Check processing steps

**Expected Behavior:**
- Skill is selected automatically
- All 10 rows processed
- JSON output generated
- No errors reported

**Pass/Fail:**
- [ ] Skill selected without prompting
- [ ] All 10 rows in output
- [ ] JSON format correct
- [ ] All fields transformed correctly
```

**Test 2: Error Handling**
```markdown
## Test: Missing Required Fields

**Goal:** Verify skill catches validation errors

**Setup:**
- File: `invalid_invoices.csv`
- 5 rows missing 'date' field
- 3 rows missing 'amount' field

**Steps:**
1. Ask Claude to "Process invalid_invoices.csv"
2. Check if validation catches issues
3. Review error messages

**Expected Behavior:**
- Validation runs before processing
- Specific rows and fields identified
- Clear error messages provided
- Processing stops (doesn't continue with invalid data)

**Pass/Fail:**
- [ ] Validation catches all issues
- [ ] Error messages are specific
- [ ] Affected rows are identified
- [ ] Processing does not continue
```

### Testing Checklist

Before finalizing a skill:

- [ ] **Happy path test**: Standard use case works correctly
- [ ] **Edge case test**: Unusual but valid inputs handled
- [ ] **Error handling test**: Invalid inputs caught and reported
- [ ] **Large data test**: Performance acceptable with real data volumes
- [ ] **Fresh session test**: Works with no prior context or hints
- [ ] **Keyword test**: Claude finds skill with natural language requests
- [ ] **Instructions test**: Claude follows instructions accurately
- [ ] **Example test**: Examples are correct and helpful

## Cross-Model Compatibility

Skills should work across Haiku, Sonnet, and Opus models.

### Model Capability Differences

**Haiku (fastest, most cost-effective):**
- Best for: Simple, well-defined tasks
- Needs: Very clear instructions, explicit steps
- Struggles with: Complex reasoning, ambiguous tasks

**Sonnet (balanced):**
- Best for: Standard workflows, moderate complexity
- Needs: Clear goals, some flexibility
- Handles: Most typical use cases well

**Opus (most capable):**
- Best for: Complex analysis, creative tasks
- Needs: High-level goals, freedom to approach
- Handles: Ambiguous or complex requirements

### Writing for Multiple Models

**Strategy 1: Provide both high-level and detailed guidance**

```markdown
## Data Analysis

**High-level goal:** Identify patterns in user behavior data

**Detailed approach:**
1. Load data and check completeness
2. Calculate key metrics: daily active users, retention rate, feature usage
3. Identify trends: increasing/decreasing patterns, seasonality
4. Find anomalies: unusual spikes or drops
5. Present findings with supporting data

Use the detailed approach if you need structure, or adapt based on
the specific data and questions.
```

**Why this works:**
- Opus can work from high-level goal
- Haiku can follow detailed approach
- Sonnet can adapt as needed

**Strategy 2: Layered complexity**

```markdown
## Email Response

**Basic template** (always acceptable):
\`\`\`
Thank you for your [request type].

[1-2 sentences addressing their specific question]

[Next steps or additional information]

Let me know if you have questions.
\`\`\`

**Enhanced approach** (if appropriate):
- Match tone to customer's message
- Add relevant context or resources
- Anticipate follow-up questions
- Personalize based on customer history

Use the basic template as a baseline, enhance if context supports it.
```

**Why this works:**
- Basic template always works (safe for all models)
- Enhancement is optional (better models can improve)
- Clear baseline prevents failures

### Testing Across Models

**Practical testing approach:**

1. **Test with Haiku first**
   - If Haiku succeeds, other models will too
   - Identifies unclear instructions
   - Forces precision

2. **Verify with Sonnet**
   - Standard use cases
   - Typical complexity
   - Balanced approach

3. **Check with Opus**
   - Complex scenarios
   - Creative applications
   - Edge cases

**What to validate:**
- [ ] Haiku follows instructions correctly
- [ ] Sonnet adapts appropriately
- [ ] Opus uses freedom effectively
- [ ] All models produce acceptable results
- [ ] No model fails or makes critical errors

### Model-Specific Notes

When necessary, include model-specific guidance:

```markdown
## Complex Analysis Task

This task requires nuanced reasoning and pattern recognition.

**Note:** This skill is optimized for Sonnet or Opus models. If using
Haiku, expect to provide more specific guidance or break the task into
smaller steps.
```

## Final Validation

Before marking skill as complete:

- [ ] All items in complete quality checklist verified
- [ ] Common mistakes reviewed and avoided
- [ ] Tested with realistic scenarios (not just artificial examples)
- [ ] Feedback from initial usage incorporated
- [ ] Documentation is clear and actionable
- [ ] Cross-model compatibility considered
- [ ] Test scenarios documented
- [ ] Ready for real-world use

Remember: Skills improve through iteration based on real usage, not perfect design up front.
