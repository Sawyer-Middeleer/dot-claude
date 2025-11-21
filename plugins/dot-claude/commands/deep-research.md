# Deep Research Command

Conducts comprehensive research on complex topics with technical rigor, synthesizing multiple sources including academic papers, technical documentation, industry reports, and practitioner insights.

## Usage

```
/deep-research [topic]
```

## Purpose

Execute a systematic research workflow that:
- Clarifies research parameters through structured dialog
- Creates a detailed research plan
- Conducts parallel source investigation
- Synthesizes findings into comprehensive documentation
- Produces research-ready artifacts with proper citations

## Prerequisites

- Access to web search and fetch capabilities
- Sufficient time for thorough investigation (varies by depth)

## Workflow Steps

### Phase 0: Topic Identification

**If the user invoked the command without a topic** (e.g., just `/deep-research`):

Use the **AskUserQuestion** tool to ask:
- **Question**: "What topic or research question would you like to investigate?"
- **Header**: "Research Topic"
- Provide a text input for the user to describe their research interest

**If the user provided a topic** (e.g., `/deep-research distributed systems patterns`):

- Proceed directly to Phase 1 with the provided topic

### Phase 1: Initial Clarification

Use the **AskUserQuestion** tool to gather structured feedback on research parameters.

Ask the user to clarify:

1. **Research purpose**: What is the goal of this research/what angles should be explored? Suggest options contextually. (use AskUserQuestion with multiSelect: true)
2. **Scope boundaries**: What should be included/excluded? Any constraints? Suggest options contextually. (use AskUserQuestion with multiSelect: true)
3. **Source preferences**: Which types are most valuable? (use AskUserQuestion with multiSelect: true)
   - Academic papers and peer-reviewed research
   - Technical documentation / Industry whitepapers
   - Technical blogs from recognized experts
   - Practitioner discussions (Reddit, X, LinkedIn, HackerNews)
   - Company documentation and case studies
4. **Research depth**: How many sources? (use AskUserQuestion with options for Light: 6-8, Medium: 9-14, Deep: 15+)

### Phase 2: Planning & Approval

**Brief preliminary investigation:**

- Search for 2-3 representative sources
- Identify major themes, terminology, key authors/organizations
- Note unexpected angles or adjacent topics worth exploring

**Create research plan:**

1. Use template from `plugins/dot-claude/commands/templates/research-plan.md`
2. Ask the user where they would like you to save your work or if you should create a new working directory
3. Create `/[RESEARCH TOPIC]` inside the working directory
4. Create and save your research plan to `[YOUR WORKING DIRECTORY]/[RESEARCH TOPIC]/plan.md`

**Plan structure:**

- **Research angles**: 3-5 core sub-questions or perspectives
- **Source strategy**: Mix of source types with target quantities based on user preferences
- **Analysis approach**: Synthesis method (thematic, comparative, chronological)
- **Expected deliverables**: Final format and supporting documentation
- **Potential challenges**: Known gaps, access issues, complexity factors

---

### Phase 3: Research Execution & Synthesis

#### Overview

Research is conducted by spawning multiple researcher subagents simultaneously—one for each source topic. Each researcher independently fetches, analyzes, and creates a comprehensive summary of their assigned source. After all researchers complete, you synthesize their findings into a unified research document.

**Research approach:**
- All sources researched simultaneously for faster execution
- Research follows the initial research plan established in Phase 2
- Comprehensive individual summaries from each researcher
- Final synthesis integrates all findings after research is complete

#### Step 1: Setup

1. Create synthesis file from template: `plugins/dot-claude/commands/templates/research-synthesis.md`
2. Save to: `[YOUR WORKING DIRECTORY]/[RESEARCH TOPIC]/synthesis.md`
3. Create summaries directory: `[YOUR WORKING DIRECTORY]/[RESEARCH TOPIC]/summaries/`

#### Step 2: Identify Research Topics

From the approved research plan, identify specific search queries to research. Aim for target source count from Phase 1 (6-8 for Light, 9-14 for Medium, 15+ for Deep).

**For each research angle in the plan**, identify 2-4 specific search queries to investigate:

Example:

- Research Angle: "Performance patterns in distributed systems"
  - Query 1: "Kubernetes horizontal pod autoscaling best practices"
  - Query 2: "Netflix microservices scaling strategies"
  - Query 3: "Load balancing algorithms for distributed systems"

Create a list of N search queries to research (where N = target source count).

#### Step 3: Find and Validate URLs

**CRITICAL: You must identify and validate specific URLs before spawning any researcher subagents.**

For each search query from Step 2:

1. Use WebSearch to find relevant sources
2. Identify a specific URL from the search results that best addresses the query
3. Use WebFetch with a simple prompt (e.g., "Extract the title and first paragraph") to verify each URL is accessible
4. If a URL is not accessible, find an alternative URL for that query and validate it
5. Compile a list of N validated URLs (one per query)

**Do not proceed to Step 4 until you have N validated, accessible URLs.**

Document the validated URLs clearly, noting which research angle and query each URL addresses.

#### Step 4: Spawn Researcher Subagents in Parallel

**CRITICAL: Use a single message with multiple Task tool calls to execute all researchers in parallel.**

For each validated URL from Step 3, spawn a researcher subagent using the Task tool:

```
Task tool parameters for each researcher:
- subagent_type: "dot-claude-agents:research-analyst"
- description: "Research [brief topic description]"
- prompt: "You are executing the researcher subagent role defined in plugins/dot-claude/agents/research-analyst.md.

Your assignment:
- Source URL: [VALIDATED_URL]
- Research focus: [specific research angle from plan]
- Research purpose: [overall research goal from Phase 1]
- Working directory: [YOUR WORKING DIRECTORY]/[RESEARCH TOPIC]

Follow the instructions in plugins/dot-claude/agents/research-analyst.md to:
1. Fetch the source using WebFetch on the provided URL
2. Conduct deep analysis
3. Create comprehensive summary using plugins/dot-claude/skills/analyzing-source/templates/article-summary.md
4. Save to: [YOUR WORKING DIRECTORY]/[RESEARCH TOPIC]/summaries/[descriptive-filename].md

Provide a brief confirmation when complete with the key insights discovered."
```

**Execute all N researchers in parallel by including N Task tool calls in a single message.**

#### Step 5: Wait for Completion

All researcher subagents will execute in parallel. Wait for all to complete before proceeding.

#### Step 6: Review Researcher Outputs

Once all researchers have completed:

1. List all summary files created in `[YOUR WORKING DIRECTORY]/[RESEARCH TOPIC]/summaries/`
2. Read each source summary file to understand what each researcher discovered
3. Note the range of perspectives, key themes, contradictions, and gaps

#### Step 7: Build Comprehensive Synthesis

Now synthesize all findings into the `synthesis.md` file:

**Synthesis approach:**

1. **Identify major themes** across all sources
   - Look for patterns that appear in multiple sources
   - Note areas of consensus and disagreement
   - Group related findings together

2. **Organize synthesis by themes**, not by source
   - Each theme section should integrate insights from multiple sources
   - Compare and contrast different perspectives
   - Build coherent narrative for each theme

3. **For each theme, include:**
   - Core insights and findings (citing source summaries using `[[filename]]`)
   - Supporting evidence and examples from multiple sources
   - Areas of agreement across sources
   - Contradictions or debates (with citations)
   - Practical implications
   - Remaining gaps or open questions

4. **Draw evidence-based conclusions**
   - Only make claims supported by multiple sources
   - Note confidence level based on source quality and consensus
   - Identify areas requiring further research

5. **Create executive summary**
   - Synthesize key findings across all themes
   - Highlight most important insights
   - Note limitations of the research

**Synthesis writing principles:**

- Be comprehensive yet concise—capture all important insights without redundancy
- Cite liberally using `[[source-filename]]` format
- Make connections explicit between different sources and themes
- Distinguish between well-supported findings and tentative conclusions
- Note source quality in your assessments (per the "Evidence Quality Assessment" sections in summaries)

#### Step 8: Quality Review

Verify synthesis quality:

- [ ] All major themes from source summaries are represented
- [ ] Synthesis is organized thematically, not source-by-source
- [ ] Key findings are supported by citations to source summaries
- [ ] Contradictions and debates are identified and explained
- [ ] Conclusions are proportional to evidence strength
- [ ] Executive summary captures essential findings
- [ ] Writing is clear, precise, and information-dense
- [ ] All internal links work
- [ ] YAML frontmatter is complete

#### Step 9: Deliver Results

**Inform user:**
- Location of synthesis and all source summaries
- Summary of major findings and themes
- Note any limitations (inaccessible sources, areas with thin coverage, etc.)
- Highlight any unexpected or particularly significant discoveries

#### Research Approach Characteristics

**Characteristics:**
- Fast execution through simultaneous source investigation
- Efficient use of compute resources
- Requires well-defined research questions and angles upfront

**Best suited for:**
- Well-understood topics with clear structure
- Situations where research angles can be predetermined
- Time-sensitive research needs
- Cases where comprehensive breadth is essential

**Considerations:**
- Research plan should be thorough since pivoting mid-research isn't supported
- Initial source topic selection is critical for comprehensive coverage

---

### Source Quality Guidelines

**Include if:**

- Provides unique insights or data
- Represents authoritative voice
- Offers contrarian or edge perspectives
- Contains primary research or original analysis

## Verification

Upon completion:

1. Confirm all deliverables are created:
   - `plan.md` with research strategy
   - `synthesis.md` with thematic analysis
   - `summaries/` directory with individual source analyses
2. Verify all internal citations link correctly
3. Check that synthesis is organized thematically, not by source
4. Ensure conclusions are proportional to evidence

## Expected Deliverables

```
[YOUR WORKING DIRECTORY]/
└── [RESEARCH TOPIC]/
    ├── summaries/
    │   ├── source-1-summary.md
    │   └── source-2-summary.md
    ├── plan.md
    └── synthesis.md
```

## Notes

- This command orchestrates multiple `research-analyst` subagents for parallel source investigation
- Templates are located in `plugins/dot-claude/commands/templates/`
- The `analyzing-source` skill provides the analysis framework used by research-analyst
- Research quality depends on source accessibility and search result quality
