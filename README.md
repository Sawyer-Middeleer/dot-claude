# Dot Claude

A unified plugin for Claude Code with actually useful skills, agents, and commands - focused on research, analysis, and skill creation.

## Installation

Add the marketplace to your Claude Code instance:

```claude
/plugin marketplace add Sawyer-Middeleer/dot-claude
```

Then install the plugin:

```claude
/plugin install dot-claude
```

That's it! You now have access to all commands, skills, and agents.

## What's Included

### Commands

#### `/deep-research`
Conducts comprehensive research on complex topics with technical rigor, synthesizing multiple sources including academic papers, technical documentation, industry reports, and practitioner insights.

**Features:**
- Structured clarification dialog for research parameters
- Two research modes: Adaptive (sequential) and Parallel (fast)
- Progressive synthesis with thematic organization
- Automated source analysis via research-analyst subagent
- Produces comprehensive documentation with proper citations

**Usage:**
```claude
/deep-research [your topic]
```

**Output:**
- `plan.md` - Research strategy and angles
- `synthesis.md` - Thematic analysis with citations
- `summaries/` - Individual source analyses

### Skills

#### `creating-skills`
Creates high-quality Claude skills following official best practices, with quality-of-life improvements. Includes guided scoping dialog, progressive disclosure patterns, and quality validation.

#### `analyzing-source`
Conducts in-depth analysis of specific sources, producing comprehensive summaries for research synthesis. Creates detailed, information-dense summaries suitable for integration into larger research projects.

**Note:** This skill is primarily used by the `research-analyst` agent within the `/deep-research` command workflow.

### Agents

#### `research-analyst`
Expert research analyst that investigates specific sources as part of larger research projects. Performs deep analysis and creates comprehensive summaries.

**Technical details:**
- Model: Haiku (optimized for speed and cost)
- Skills: `analyzing-source`
- Permission Mode: default

**Note:** This agent is automatically spawned by the `/deep-research` command for parallel source analysis.

## How It Works Together

The plugin provides an integrated research system:

1. **`/deep-research` command** - User-facing interface that orchestrates the research workflow
2. **`research-analyst` agent** - Spawned in parallel to analyze individual sources
3. **`analyzing-source` skill** - Provides the analysis framework used by research-analyst
4. **`creating-skills` skill** - Meta-skill for creating new Claude Code skills

All components work together seamlessly as part of a single plugin.

## Example Workflow

```claude
/deep-research distributed systems scaling patterns

# Claude will:
# 1. Ask clarifying questions about research goals and preferences
# 2. Create a research plan with defined angles
# 3. Spawn multiple research-analyst agents in parallel (or sequentially)
# 4. Each agent uses analyzing-source to create detailed summaries
# 5. Synthesize findings into thematic analysis
# 6. Deliver comprehensive research documentation
```
