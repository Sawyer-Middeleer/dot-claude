---
name: creating-skills
description: Creates high-quality Claude skills following official best practices. Use when the user asks to create a new skill, improve an existing skill, or needs guidance on skill authoring. Includes proper structure, naming conventions, progressive disclosure patterns, and quality validation.
---

# Creating High-Quality Claude Skills

Use this skill when creating or improving Claude skills to ensure they follow official best practices and work effectively across all Claude models.

## Core Principles

**Conciseness**: Skills share the context window. Only include information Claude doesn't already have.

**Appropriate Freedom**: Match instruction detail to task needs:

- **High freedom**: Multiple valid approaches (use text instructions)
- **Medium freedom**: Preferred patterns with flexibility (use pseudocode/guidance)
- **Low freedom**: Exact sequences required (use specific bash/python scripts)

**Progressive Disclosure**: Keep SKILL.md under 500 lines. Split content into reference files when approaching this limit. All references must be one level deep.

## Phase 1: Initial Clarification (CRITICAL)

**BEFORE planning or writing the skill**, use the **AskUserQuestion** tool to gather structured feedback on skill requirements.

Ask the user to clarify (only if needed):

1. **Instruction detail level**: How much freedom should Claude have? (use AskUserQuestion with single select)
   - High freedom - Multiple valid approaches, text instructions only
   - Medium freedom - Preferred patterns with flexibility, pseudocode/guidance
   - Low freedom - Exact sequences required, specific scripts

2. **Success criteria**: How will success be measured? Suggest options contextually based on skill type. (use AskUserQuestion with multiSelect: true)

3. **Special considerations**: Are there any special requirements? Suggest options contextually based on skill type. (use AskUserQuestion with multiSelect: true)

Feel free to include 1-2 additional multiSelect questions if it's essential to clarify workflow steps, inputs and outputs, or other materials to include in the skill. You can write the questions and answer options based on context if you want to do this.

## Phase 2: Design & Planning

After gathering requirements in Phase 1, design the skill structure:

1. **Choose skill name**
   - [ ] Use gerund form (verb + -ing, max 64 chars)
   - [ ] Lowercase letters, numbers, hyphens only
   - [ ] Avoid: `helper`, `utils`, or words containing "anthropic"/"claude" as these are reserved words

2. **Write description**
   - [ ] Include specific keywords for discovery
   - [ ] State what the skill does and when to use it
   - [ ] Keep under 1024 chars, write in third person

3. **Plan content structure**
   - [ ] Determine if single file (< 400 lines) or progressive disclosure needed
   - [ ] Identify sections: workflow, examples, templates, validation
   - [ ] Note which parts need high/medium/low freedom based on Phase 1

4. **Generate architecture diagram**
   - [ ] Create ASCII architecture diagram showing full skill structure
   - [ ] Follow the format guidelines in [reference/architecture-diagrams.md](./reference/architecture-diagrams.md)
   - [ ] Include: phases, components, decision trees, file structure, validation flows

5. **Present plan to user**
   - Share proposed name, description, and structure outline
   - **Present the architecture diagram** for visual validation
   - Confirm alignment with their expectations
   - Adjust based on feedback before proceeding to implementation

## Phase 3: Create Content

1. **Write SKILL.md**
   - [ ] Add valid YAML frontmatter
   - [ ] Include only information Claude doesn't already have
   - [ ] Add workflows with checklists for complex tasks

2. **Add supplementary files** (if needed)
   - [ ] Create subdirectory(ies) (e.g., `examples/`, `templates/`, `scripts/`, etc)
   - [ ] Split content into focused topics (one level deep only)
   - [ ] Ensure SKILL.md remains under 500 lines

3. **Implement feedback mechanisms** (if identified in Phase 1)
   - [ ] Add validation checkpoints
   - [ ] Structure quality review loops
   - [ ] Specify error handling approaches

## Phase 4: Validate Quality

Before finalizing, run through the quality checklist:

- [ ] Valid YAML frontmatter with name and description
- [ ] Name follows conventions (gerund, ≤64 chars, lowercase/hyphens)
- [ ] Description includes usage triggers (≤1024 chars)
- [ ] SKILL.md under 500 lines
- [ ] Supporting files (if any) are one level deep
- [ ] Only includes information Claude doesn't already have
- [ ] Consistent terminology throughout
- [ ] Concrete examples provided
- [ ] Scripts (if any) have explicit error handling
- [ ] File paths use forward slashes
- [ ] Dependencies documented

## YAML Frontmatter (Required)

Every skill must start with:

```yaml
---
name: skill-name-here
description: Clear description with keywords, functionality, and usage triggers for discovery.
---
```

**Name requirements:**
- Lowercase letters, numbers, hyphens only (max 64 chars)
- Use gerund form: `processing-pdfs`, `analyzing-spreadsheets`
- Avoid: `helper`, `utils`, or words containing "anthropic"/"claude"

**Description requirements:**
- Non-empty, no XML tags (max 1024 chars)
- Write in third person
- Include specific keywords and when to use this skill
- Think: "How will this be found among 100+ skills?"

## Content Organization

### Single File vs. Progressive Disclosure

**Use single SKILL.md when:**
- Content is under 400 lines
- Information is tightly related
- Users need everything at once

**Use progressive disclosure when:**
- Approaching 500 lines
- Content has distinct topics
- Some details are conditional/advanced

**Progressive disclosure patterns:**

```
SKILL.md (overview + core workflow)
└── reference/
    ├── topic-one.md
    ├── topic-two.md
    └── advanced-features.md
```

**Critical rule:** Never nest references. All reference files must be directly under a single subdirectory.

## Content Principles

Core principles for writing effective skill content: conciseness, freedom levels, and progressive disclosure.

## Conciseness

Skills share the context window with conversation history and other skills. Every word must earn its place.

### Challenge Every Detail

Before including any information, ask:

- Is this essential for the task?
- Could this be inferred from context?
- Does this duplicate instruction that already exists?

**Example - Essential information only:**
```markdown
## Processing CSV Files

When processing CSVs:
1. Validate column headers match expected schema
2. Check for encoding issues (default to UTF-8)
3. Handle missing values explicitly (null vs. empty string)
```

### Focus on Unique Information

Only include what Claude doesn't already know or what's specific to your use case:

**Example (unique, actionable information):**
```markdown
## Data Processing Requirements

For this workflow, use pandas with these constraints:
- Preserve original column order (df.reindex())
- Handle timestamps in Pacific timezone
- Round currency values to 2 decimal places
```

### Remove Redundant Explanations

Don't explain the same concept multiple times:

**Example:**
```markdown
## Workflow

1. **Validate Input**: Check schema and data types
2. **Process Data**: Apply transformations
3. **Generate Output**: Format and export results
```

## Common Content Patterns

### Workflows with Checklists

Break complex tasks into steps:

```markdown
1. **Analyze Input**
   - [ ] Validate format
   - [ ] Check required fields
   - [ ] Identify issues

2. **Process Data**
   - [ ] Apply transformations
   - [ ] Handle edge cases
   - [ ] Validate output
```

### Templates and Examples

Provide concrete demonstrations:

```markdown
## Example: API Request

\`\`\`python
# Use this pattern for all API calls
response = requests.post(
    url="https://api.example.com/endpoint",  # API endpoint
    json={"key": "value"},  # Request payload
    timeout=30  # Prevent hanging (API rate limits)
)
\`\`\`
```

### Feedback Loops

Implement validation cycles:

```markdown
1. Run validation
2. If errors found → review → fix → return to step 1
3. If passes → proceed
```

## Scripts and Code Guidelines

**Error handling**: Scripts should provide specific, actionable messages

```python
if df['date'].isna().any():
    missing_count = df['date'].isna().sum()
    print(f"Error: {missing_count} rows have missing dates.")
    print(f"Affected rows: {df[df['date'].isna()].index.tolist()}")
    print("Solution: Remove rows or fill with interpolated values.")
```

**File paths**: Always use forward slashes for cross-platform compatibility
- ✓ `data/input/file.csv`
- ✗ `data\input\file.csv`

**Dependencies**: Document required packages and explain configuration values

**MCP tools**: Use fully qualified names: `ServerName:tool_name`

See [reference/scripts-and-code.md](./reference/scripts-and-code.md) for comprehensive guidance.

## Quality Checklist (Essential)

Before finalizing a skill:

**Structure:**

- [ ] Valid YAML frontmatter with name and description
- [ ] Name follows conventions (gerund, ≤64 chars, lowercase/hyphens)
- [ ] Description includes usage triggers (≤1024 chars)
- [ ] SKILL.md under 500 lines
- [ ] Reference files (if any) are one level deep

**Content:**

- [ ] Consistent terminology throughout
- [ ] Concrete examples provided
- [ ] Workflows use checkboxes for complex tasks
- [ ] No time-sensitive information

**Technical:**

- [ ] Scripts have explicit error handling
- [ ] File paths use forward slashes
- [ ] Dependencies documented
- [ ] No "magic numbers" (all config explained)

## Reference Files

For detailed guidance on specific topics:

- [Scripts and Code](./reference/scripts-and-code.md) - Error handling, dependencies, configuration
- [Architecture Diagrams](./reference/architecture-diagrams.md) - Skill architecture diagram guidelines