# Architecture Diagrams

Create an ASCII architecture diagram in Phase 2 to visualize the skill structure before implementation.

## Purpose

Show the complete skill architecture:

- Flow from user request to deliverable output
- All phases with their key components
- Decision points and branching logic
- File structure and organization
- How parts connect and interact

## Structure Requirements

### Essential Components

1. **Header** - Skill name and purpose
2. **Core principles layer** - Conciseness, freedom level, progressive disclosure
3. **Phase sections** - Each phase with key components and steps
4. **Flow indicators** - Arrows showing progression (│, ▼, →, ←)
5. **Decision trees** - Where skill has branching logic
6. **File structure** - Output deliverable with directory tree
7. **Validation section** - Quality checks or review loops
8. **Reference layer** - If using progressive disclosure

### Box Drawing Characters

```
╔═══╗  Double-line (major sections/headers)
║   ║
╚═══╝

┌───┐  Single-line (components)
│   │
└───┘

│ ▼ → ←  Flow arrows
```

### Critical: Border Alignment

All right-side borders must align vertically. Test in monospace terminal before presenting.

## Template

```
╔═══════════════════════════════════════════════════════════╗
║                   SKILL-NAME ARCHITECTURE                 ║
╚═══════════════════════════════════════════════════════════╝

                      ┌─────────────┐
                      │ USER REQUEST│
                      └──────┬──────┘
                             ▼
╔═══════════════════════════════════════════════════════════╗
║ CORE PRINCIPLES                                           ║
║ ┌─────────┐  ┌─────────┐  ┌─────────┐                     ║
║ │Principle│  │Principle│  │Principle│                     ║
║ └─────────┘  └─────────┘  └─────────┘                     ║
╚═══════════════════════════════════════════════════════════╝
                             │
                             ▼
╔═══════════════════════════════════════════════════════════╗
║ PHASE 1: [NAME]                                           ║
║ ┌─────────────┐  ┌─────────────┐                          ║
║ │ Component 1 │  │ Component 2 │                          ║
║ └─────────────┘  └─────────────┘                          ║
╚═══════════════════════════════════════════════════════════╝
                             │
                             ▼
[Additional phases...]

╔═══════════════════════════════════════════════════════════╗
║ OUTPUT DELIVERABLE                                        ║
║ ┌───────────────────────────────────────────────────┐     ║
║ │ skill-name/                                       │     ║
║ │ ├── SKILL.md                                      │     ║
║ │ └── reference/ (optional)                         │     ║
║ └───────────────────────────────────────────────────┘     ║
╚═══════════════════════════════════════════════════════════╝
```

## Content Guidelines

### Emphasize

- User interaction points
- Decision points with clear conditions
- Validation and quality checks
- Flow of data
- Skill, subagent, tool, etc dependencies
- Tool usage (Read, Write, Edit, etc.)

### Keep concise

- High-level components only, not every detail
- Show flow and structure, not implementation
- Fit diagram in terminal window

### Adapt by skill type

- Workflow: Sequential phases, validation checkpoints
- Analysis: Input validation, feedback loops, quality checks
- Content generation: Templates, parameters, formatting
- Research: Source discovery, synthesis approach, citations
