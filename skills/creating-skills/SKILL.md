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
- **Low freedom**: Exact sequences required (use specific scripts)

**Progressive Disclosure**: Keep SKILL.md under 500 lines. Split content into reference files when approaching this limit. All references must be one level deep.

## Phase 1: Initial Clarification (CRITICAL)

**BEFORE planning or writing the skill**, use the **AskUserQuestion** tool to gather structured feedback on skill requirements.

Ask the user to clarify:

1. **Skill type**: What category best describes this skill? (use AskUserQuestion with multiSelect: true)
   - Workflow automation (multi-step processes with specific sequences)
   - Analysis and validation (checking, reviewing, assessing content)
   - Content generation (creating documents, reports, code)
   - Data transformation (converting, formatting, restructuring)
   - Research and investigation (gathering, synthesizing information)
   - Other (user will specify)

2. **Instruction detail level**: How much freedom should Claude have? (use AskUserQuestion with single select)
   - High freedom - Multiple valid approaches, text instructions only
   - Medium freedom - Preferred patterns with flexibility, pseudocode/guidance
   - Low freedom - Exact sequences required, specific scripts

3. **Core workflow elements**: What should the skill include? (use AskUserQuestion with multiSelect: true)
   - Step-by-step procedures with checklists
   - Validation and error handling steps
   - Templates or examples
   - Quality review loops
   - Decision points and branching logic
   - Tool or API integrations

4. **Success criteria**: How will success be measured? Suggest options contextually based on skill type. (use AskUserQuestion with multiSelect: true)

5. **Special considerations**: Are there any special requirements? (use AskUserQuestion with multiSelect: true)
   - Security or compliance requirements
   - Performance constraints
   - Integration with specific tools/APIs
   - Existing code standards to follow
   - Edge cases or error scenarios
   - None - straightforward implementation

**After gathering responses**, ask follow-up questions as needed to clarify:
- Specific workflow steps and their sequence
- Input/output formats at each stage
- Examples of good vs. bad output
- Any templates or reference materials to include

**DO NOT** make assumptions or proceed with generic patterns. Each skill is unique—these questions ensure you build exactly what the user needs.

## Phase 2: Design & Planning

After gathering requirements in Phase 1, design the skill structure:

1. **Choose skill name**
   - [ ] Use gerund form (verb + -ing, max 64 chars)
   - [ ] Lowercase letters, numbers, hyphens only
   - [ ] Avoid: `helper`, `utils`, or words containing "anthropic"/"claude"

2. **Write description**
   - [ ] Include specific keywords for discovery
   - [ ] State what the skill does and when to use it
   - [ ] Keep under 1024 chars, write in third person
   - [ ] Think: "How will this be found among 100+ skills?"

3. **Plan content structure**
   - [ ] Determine if single file (< 400 lines) or progressive disclosure needed
   - [ ] Identify sections: workflow, examples, templates, validation
   - [ ] Note which parts need high/medium/low freedom based on Phase 1

4. **Generate architecture diagram**
   - [ ] Create ASCII architecture diagram showing full skill structure
   - [ ] Follow the format guidelines in [reference/architecture-diagrams.md](./reference/architecture-diagrams.md)
   - [ ] Include: phases, components, decision trees, file structure, validation flows
   - [ ] Ensure borders are properly aligned

5. **Present plan to user**
   - Share proposed name, description, and structure outline
   - **Present the architecture diagram** for visual validation
   - Confirm alignment with their expectations
   - Adjust based on feedback before proceeding to implementation

## Phase 3: Create Content

1. **Write SKILL.md**
   - [ ] Add valid YAML frontmatter
   - [ ] Include only information Claude doesn't already have
   - [ ] Use appropriate instruction detail level from Phase 1
   - [ ] Add workflows with checklists for complex tasks
   - [ ] Include concrete examples and templates

2. **Add reference files** (if needed)
   - [ ] Create single subdirectory (e.g., `reference/`)
   - [ ] Split content into focused topics (one level deep only)
   - [ ] Ensure SKILL.md remains under 500 lines

3. **Implement feedback mechanisms** (if identified in Phase 1)
   - [ ] Add validation checkpoints
   - [ ] Include quality review loops
   - [ ] Specify error handling approaches

## Phase 4: Validate Quality

Before finalizing, run through the quality checklist:

- [ ] Valid YAML frontmatter with name and description
- [ ] Name follows conventions (gerund, ≤64 chars, lowercase/hyphens)
- [ ] Description includes usage triggers (≤1024 chars)
- [ ] SKILL.md under 500 lines
- [ ] Reference files (if any) are one level deep
- [ ] Only includes information Claude doesn't already have
- [ ] Consistent terminology throughout
- [ ] Concrete examples provided
- [ ] Scripts (if any) have explicit error handling
- [ ] File paths use forward slashes
- [ ] Dependencies documented

**Suggest to user:** After the skill is created, they may want to:
- Test with 3+ real use cases to verify instructions are clear
- Iterate based on observed behavior (add detail where needed, remove redundancy)
- Refine the appropriate freedom level if Claude struggles or is too constrained

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

See [reference/structure-and-naming.md](./reference/structure-and-naming.md) for detailed guidance.

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

See [reference/content-principles.md](./reference/content-principles.md) for detailed patterns.

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

See [reference/workflows-and-patterns.md](./reference/workflows-and-patterns.md) for more patterns.

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
- [ ] Only includes information Claude doesn't already have
- [ ] Consistent terminology throughout
- [ ] Concrete examples provided
- [ ] Workflows use checkboxes for complex tasks
- [ ] No time-sensitive information

**Technical:**
- [ ] Scripts have explicit error handling
- [ ] File paths use forward slashes
- [ ] Dependencies documented
- [ ] No "magic numbers" (all config explained)

**Testing:**
- [ ] Works for intended use cases
- [ ] Instructions clear and actionable

See [reference/quality-checklist.md](./reference/quality-checklist.md) for complete checklist and common mistakes.

## Iterative Development

1. **Create test scenarios first** (3+ real use cases)
2. **Build minimal version** with core functionality
3. **Test with fresh Claude instance** on actual tasks
4. **Iterate based on observed behavior**, not assumptions
5. **Add detail where Claude struggled**, remove redundant instructions

## Reference Files

For detailed guidance on specific topics:

- [Structure and Naming](./reference/structure-and-naming.md) - Frontmatter, naming conventions, descriptions
- [Content Principles](./reference/content-principles.md) - Conciseness, freedom levels, progressive disclosure
- [Workflows and Patterns](./reference/workflows-and-patterns.md) - Checklists, templates, examples, feedback loops
- [Scripts and Code](./reference/scripts-and-code.md) - Error handling, dependencies, configuration
- [Quality Checklist](./reference/quality-checklist.md) - Complete validation checklist and common mistakes

## Quick Examples

**Good skill name:** `processing-invoices` (gerund, specific, clear)
**Poor skill name:** `invoice-helper` (not gerund, vague)

**Good description:** "Processes vendor invoices to extract line items, validate totals, and flag discrepancies. Use when analyzing invoice documents, verifying billing accuracy, or preparing payment data."

**Poor description:** "Helps with invoices" (too vague, no triggers)

Remember: The best skills are concise, focused, and immediately useful. Start simple and iterate based on real usage.
