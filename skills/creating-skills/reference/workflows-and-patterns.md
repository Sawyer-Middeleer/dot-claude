# Workflows and Patterns

Common content patterns for skills: workflows with checklists, feedback loops, templates, examples, and conditional workflows.

## Table of Contents

- [Workflows with Checklists](#workflows-with-checklists)
- [Feedback Loops](#feedback-loops)
- [Templates](#templates)
- [Examples](#examples)
- [Conditional Workflows](#conditional-workflows)
- [Visual Analysis](#visual-analysis)
- [Intermediate Outputs](#intermediate-outputs)

## Workflows with Checklists

Break complex operations into sequential steps with checkboxes for tracking progress.

### Basic Workflow Pattern

```markdown
## Data Processing Workflow

1. **Analyze Input**
   - [ ] Validate file format
   - [ ] Check for required fields
   - [ ] Identify data quality issues

2. **Transform Data**
   - [ ] Apply mapping rules
   - [ ] Handle edge cases
   - [ ] Validate transformations

3. **Generate Output**
   - [ ] Format according to template
   - [ ] Verify completeness
   - [ ] Provide summary
```

### Why Checklists Work

**Benefits:**
- Clear progress tracking
- Nothing gets forgotten
- Easy to resume after interruption
- User can see what's been completed

**When to use:**
- Multi-step processes
- Tasks with dependencies
- Operations requiring verification
- Complex workflows where order matters

### Nested Checklists

For complex tasks, use hierarchical structure:

```markdown
## Application Deployment Workflow

1. **Pre-Deployment**
   - [ ] Code review completed
   - [ ] Tests passing
   - [ ] Documentation updated
   - [ ] Security scan clean

2. **Deployment**
   - [ ] Backup current version
   - [ ] Deploy to staging
     - [ ] Verify staging deployment
     - [ ] Run smoke tests
     - [ ] Check monitoring
   - [ ] Deploy to production
     - [ ] Verify production deployment
     - [ ] Run smoke tests
     - [ ] Monitor error rates

3. **Post-Deployment**
   - [ ] Confirm all services healthy
   - [ ] Update status page
   - [ ] Notify stakeholders
   - [ ] Schedule post-mortem
```

### Checklist Best Practices

**Be specific:**
❌ `- [ ] Check data`
✓ `- [ ] Validate all required fields are non-null`

**Make items actionable:**
❌ `- [ ] Think about security`
✓ `- [ ] Verify authentication tokens are not expired`

**One action per item:**
❌ `- [ ] Test the app, fix bugs, and deploy`
✓
```markdown
- [ ] Run test suite
- [ ] Fix failing tests
- [ ] Verify all tests pass
- [ ] Deploy to staging
```

## Feedback Loops

Implement validator → fix errors → repeat cycles to catch problems early.

### Basic Feedback Loop

```markdown
## Quality Assurance Loop

1. Run validation check
2. If errors found:
   - Review specific issues
   - Apply corrections
   - Return to step 1
3. If validation passes, proceed to next stage
```

### Detailed Feedback Loop Example

```markdown
## Data Validation Loop

**Phase 1: Initial Validation**
1. Run schema validation
2. Collect all schema errors
3. If errors found → fix and repeat Phase 1
4. If no errors → proceed to Phase 2

**Phase 2: Business Rules Validation**
1. Run business rules checks
2. Collect all rule violations
3. If violations found → fix and repeat Phase 2
4. If no violations → proceed to Phase 3

**Phase 3: Cross-Field Validation**
1. Check relationships between fields
2. Verify referential integrity
3. If issues found → fix and repeat Phase 3
4. If all checks pass → data is valid

**Note:** Each phase must complete successfully before moving to the next.
```

### Progressive Validation Pattern

Validate early and often to catch errors fast:

```markdown
## Progressive Validation Approach

1. **Quick Checks First** (fail fast)
   - File exists and is readable?
   - File format correct (CSV, JSON, etc.)?
   - File size reasonable?
   - ↻ If fails: stop and report

2. **Schema Validation** (structure)
   - Required columns present?
   - Data types correct?
   - Column names match expected?
   - ↻ If fails: fix schema and retry

3. **Data Quality Validation** (content)
   - No null values in required fields?
   - Values within expected ranges?
   - Formats correct (dates, emails, etc.)?
   - ↻ If fails: clean data and retry

4. **Business Rules Validation** (logic)
   - Business rules satisfied?
   - Relationships valid?
   - Calculations correct?
   - ↻ If fails: correct data and retry

5. **Final Verification** (comprehensive)
   - Run all checks one final time
   - Generate validation report
   - ↻ If any issues: start from relevant phase
```

### When to Use Feedback Loops

**Use feedback loops for:**
- Data validation and cleaning
- Code generation and testing
- Multi-stage transformations
- Quality assurance processes
- Iterative refinement tasks

**Don't use for:**
- Simple one-step operations
- Tasks with no validation needed
- Operations that can't be retried

## Templates

Provide strict structure for format-critical outputs; flexible guidance where adaptation helps.

### When to Provide Templates

**Use strict templates for:**
- Reports with required sections
- API requests with exact format
- Configuration files with specific syntax
- Documents with compliance requirements

**Use flexible templates for:**
- Exploratory analysis
- Creative content
- Adaptable workflows

### Strict Template Example

```markdown
## Incident Report Template

Use this exact structure for all incident reports:

\`\`\`markdown
# Incident Report: [INCIDENT-ID]

## Summary
[2-3 sentence overview of what happened]

## Timeline
- **[TIME]**: [Event description]
- **[TIME]**: [Event description]

## Impact
- **Users affected**: [Number/percentage]
- **Duration**: [Start time] to [End time]
- **Services impacted**: [List]

## Root Cause
[Detailed explanation of underlying cause]

## Resolution
[Steps taken to resolve the incident]

## Action Items
1. [ ] [Action item with owner and due date]
2. [ ] [Action item with owner and due date]

## Lessons Learned
- [Key takeaway 1]
- [Key takeaway 2]
\`\`\`
```

### Flexible Template Example

```markdown
## Data Analysis Template

Use this as a starting structure, adapting as needed:

\`\`\`markdown
# Analysis: [Topic]

## Overview
Brief description of what was analyzed and why

## Key Findings
- Most important insights
- Unexpected patterns
- Notable anomalies

## Detailed Analysis
[Adapt this section based on your specific analysis]
- Could include statistical tests
- Visualizations
- Segment comparisons
- Trend analysis

## Recommendations
Actionable suggestions based on findings

## Appendix (optional)
- Methodology details
- Data sources
- Assumptions
\`\`\`
```

### Template with Options

```markdown
## Email Response Template

**For bug reports:**
\`\`\`
Thank you for reporting this issue.

I've confirmed the bug and [logged it as TICKET-ID / fixed it in version X.Y].

[If logged]: We'll prioritize this for the next sprint.
[If fixed]: The fix will be available in the next release.

Please let me know if you have any questions.
\`\`\`

**For feature requests:**
\`\`\`
Thank you for the suggestion!

This aligns with [roadmap item / future plans / our vision].

I've added it to our feature backlog with your feedback. We'll
[consider it for Q# / evaluate it against our priorities].

I'll update you when we have more information.
\`\`\`
```

## Examples

Include input/output pairs demonstrating desired style and detail level.

### Why Examples Matter

**Examples help Claude:**
- Understand the desired level of detail
- Match the expected style and tone
- See how to handle edge cases
- Learn domain-specific patterns

**One good example > pages of instructions**

### Example Pattern: Good vs. Poor

```markdown
## Writing Function Documentation

❌ **Poor documentation:**
\`\`\`python
def process_data(df):
    """Processes data"""
    return df.apply(lambda x: x * 2)
\`\`\`

✓ **Good documentation:**
\`\`\`python
def process_data(df: pd.DataFrame) -> pd.DataFrame:
    """
    Applies business-specific transformations to financial data.

    Args:
        df: DataFrame with columns ['amount', 'currency', 'date']
           Must contain only positive amounts in USD.

    Returns:
        DataFrame with additional 'normalized_amount' column
        containing amounts adjusted to base currency.

    Raises:
        ValueError: If amounts are negative or currency not supported.

    Example:
        >>> df = pd.DataFrame({'amount': [100, 200],
        ...                    'currency': ['USD', 'USD']})
        >>> result = process_data(df)
        >>> 'normalized_amount' in result.columns
        True
    """
    return df.apply(lambda x: x * 2)
\`\`\`
```

### Example Pattern: Input/Output Pairs

```markdown
## Data Transformation Examples

**Example 1: Basic transformation**

Input:
\`\`\`json
{
  "customer_name": "John Doe",
  "order_date": "2024-03-15"
}
\`\`\`

Output:
\`\`\`json
{
  "customerName": "John Doe",
  "orderDate": "2024-03-15T00:00:00Z",
  "processedAt": "2024-03-15T14:30:00Z"
}
\`\`\`

**Example 2: Handling missing fields**

Input:
\`\`\`json
{
  "customer_name": "Jane Smith"
}
\`\`\`

Output:
\`\`\`json
{
  "customerName": "Jane Smith",
  "orderDate": null,
  "processedAt": "2024-03-15T14:30:00Z",
  "warnings": ["Missing required field: order_date"]
}
\`\`\`
```

### Example Pattern: Progressive Complexity

Show examples from simple to complex:

```markdown
## Query Examples

**Level 1: Basic query**
\`\`\`sql
SELECT name, email FROM users WHERE active = true;
\`\`\`

**Level 2: With joins**
\`\`\`sql
SELECT u.name, u.email, o.order_date
FROM users u
JOIN orders o ON u.id = o.user_id
WHERE u.active = true;
\`\`\`

**Level 3: With aggregation and filtering**
\`\`\`sql
SELECT
    u.name,
    u.email,
    COUNT(o.id) as order_count,
    SUM(o.total) as total_spent
FROM users u
LEFT JOIN orders o ON u.id = o.user_id
WHERE u.active = true
GROUP BY u.id, u.name, u.email
HAVING COUNT(o.id) > 5
ORDER BY total_spent DESC;
\`\`\`
```

## Conditional Workflows

Guide Claude through decision points with clear branching logic.

### Basic Conditional Pattern

```markdown
## Choosing Processing Method

**If data size < 1000 rows:**
- Use direct in-memory processing
- Single-pass transformation
- Immediate validation

**If data size between 1000-100000 rows:**
- Use chunked processing (1000 rows per chunk)
- Stream transformations
- Periodic validation checkpoints

**If data size > 100000 rows:**
- Use distributed processing
- Database-backed transformation
- Batch validation with parallel workers
```

### Decision Tree Pattern

```markdown
## Error Handling Decision Tree

1. **Check error type**

   **If ValidationError:**
   - Log validation details
   - Return specific field errors to user
   - Suggest corrections
   - Do NOT retry

   **If DatabaseError:**
   - Go to step 2

   **If NetworkError:**
   - Go to step 3

   **If UnknownError:**
   - Log full stack trace
   - Alert on-call engineer
   - Return generic error to user

2. **Database Error Handling**

   **If connection timeout:**
   - Retry up to 3 times with exponential backoff
   - If still failing → Alert DBA

   **If constraint violation:**
   - Do NOT retry
   - Return specific constraint error to user

   **If deadlock:**
   - Retry once immediately
   - If fails again → Queue for later

3. **Network Error Handling**

   **If timeout:**
   - Retry with increased timeout (up to 3 attempts)

   **If connection refused:**
   - Check service health endpoint
   - If service down → Queue request
   - If service up → Alert DevOps
```

### Threshold-Based Conditionals

```markdown
## Response Time Optimization

**For response time < 100ms:**
- Current implementation is optimal
- No changes needed

**For response time 100-500ms:**
- Add database query optimization
- Consider adding indexes on frequently queried columns
- Review N+1 query patterns

**For response time 500-2000ms:**
- Implement caching layer (Redis)
- Add database read replicas
- Consider query result pagination

**For response time > 2000ms:**
- Emergency optimization required
- Enable query logging for analysis
- Consider architectural changes (async processing, microservices)
- Add monitoring and alerting
```

## Visual Analysis

For PDF or document layout analysis, use image-based approaches.

### PDF Layout Analysis

```markdown
## Analyzing Document Structure

For visual layout analysis:

1. **Convert to Images**
   - Extract each page as PNG/JPG
   - Maintain original resolution (300 DPI recommended)

2. **Visual Analysis**
   Claude can analyze images directly to identify:
   - Form fields and their locations
   - Table structures and boundaries
   - Header/footer sections
   - Multi-column layouts
   - Signature blocks
   - Logos and graphics

3. **Structure Mapping**
   Based on visual analysis, create extraction strategy:
   - Map form fields to data fields
   - Identify table extraction coordinates
   - Define section boundaries
   - Specify text extraction order

4. **Data Extraction**
   Use identified structure to extract data accurately

**Note:** Visual analysis is more reliable than text-only analysis for
complex layouts, forms, and multi-column documents.
```

## Intermediate Outputs

Implement plan-validate-execute patterns for complex batch operations.

### Plan-Validate-Execute Pattern

```markdown
## Batch Update Workflow

**Phase 1: Planning**
1. Analyze all items requiring updates
2. Generate update plan for each item
3. Save plan to `update_plan.json`
4. Do NOT execute yet

**Phase 2: Validation**
1. Review `update_plan.json`
2. Check for potential issues:
   - [ ] No duplicate updates
   - [ ] All references valid
   - [ ] Update order handles dependencies
   - [ ] Rollback plan exists
3. Fix any issues and regenerate plan
4. Get approval to proceed

**Phase 3: Execution**
1. Load approved `update_plan.json`
2. Execute updates one by one
3. Log success/failure for each
4. If failure: stop and rollback if needed
5. Generate execution report

This approach prevents batch operation errors by validating the entire
plan before making any changes.
```

### Dry-Run Pattern

```markdown
## Database Migration with Dry-Run

**Step 1: Dry-Run Mode**
\`\`\`python
# Set dry_run=True to preview changes without executing
migration_results = run_migration(dry_run=True)

print(f"Would update {migration_results['affected_rows']} rows")
print(f"Would add {len(migration_results['new_columns'])} columns")
print(f"Estimated duration: {migration_results['estimated_time']}")
\`\`\`

**Step 2: Review Dry-Run Results**
- [ ] Affected row count is expected
- [ ] No unexpected side effects
- [ ] Estimated duration is acceptable
- [ ] Rollback plan is ready

**Step 3: Execute Migration**
\`\`\`python
# Only after dry-run validation
migration_results = run_migration(dry_run=False)
\`\`\`
```

## Validation Checklist

Workflows and patterns checklist:

- [ ] Complex workflows broken into steps with checkboxes
- [ ] Feedback loops implemented for validation-heavy tasks
- [ ] Templates provided where format is critical
- [ ] Examples show input/output pairs
- [ ] Examples demonstrate desired detail level
- [ ] Conditional workflows have clear decision points
- [ ] Threshold-based conditions include specific values
- [ ] Visual analysis approaches documented where relevant
- [ ] Intermediate outputs used for complex batch operations
- [ ] Plan-validate-execute pattern for risky operations
