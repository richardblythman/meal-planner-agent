# Create CLAUDE.md Command

Generate a CLAUDE.md file for an AI agent repository through guided conversation and requirements gathering.

## Purpose

CLAUDE.md serves as an AI developer onboarding guide - providing Claude Code with the essential context, conventions, and "why" reasoning needed to work effectively within a specific agent codebase.

## Initial Response

When this command is invoked:

1. **Check if parameters were provided**:
   - If agent details, context, or existing documentation is provided, begin with that context
   - If files are referenced, read them FULLY first to understand existing context
   - If no parameters provided, respond with the default prompt below

2. **If no parameters provided**, respond with:
```
I'll help you create a CLAUDE.md file for your AI agent repository through collaborative conversation.

This file will serve as Claude Code's "onboarding guide" - providing context about:
- What this agent does and why it exists
- How the agent repository is structured and where things are
- Agent development conventions and workflows
- Key decisions and their rationale

What agent are you building? Tell me about:
- Its purpose and what problems it solves
- Who will use it or interact with it
- Any specific requirements or constraints

Don't worry about having all the details - we'll explore and define everything together!
```

Then wait for the user's input.

## Context: This is an Agent Repository

**IMPORTANT**: The CLAUDE.md being created is for an **agent repository** that builds on top of the general-purpose Claude Code agent framework.

**CRITICAL**: These repositories are **natural language codebases** - they use natural language to program the agent through Claude Code commands and CLAUDE.md instructions. There is typically **no traditional code** (no Python, JavaScript, etc.). The agent is defined entirely through:
- Natural language instructions in CLAUDE.md
- Claude Code commands (Markdown files with instructions)
- Specifications (natural language documents)
- Memory/knowledge base (natural language)

### Key Agent Infrastructure Locations

When gathering requirements, be aware that agent repositories typically include:

1. **CLAUDE.md** (the file we're creating)
   - Acts as a system prompt for Claude Code
   - Lives at repository root
   - Provides operational context and conventions
   - Written in natural language to instruct Claude

2. **Commands** (`.claude/commands/`)
   - Custom slash commands for this agent
   - Automate agent-specific workflows
   - Use Markdown format with natural language instruction blocks
   - No code - just instructions Claude follows

3. **Memory/Knowledge Base** (`memory/` or similar)
   - Persistent context and learnings
   - Agent-specific knowledge
   - Session state and history
   - All in natural language

4. **Specifications** (`specifications/`)
   - Feature specifications
   - Command specifications
   - Architecture decisions
   - All in natural language documents

## Discovery Phase

### Step 1: Understand the Agent

Ask questions to understand the agent's purpose and context:

1. **What does this agent do?**
   - What's its primary function or goal?
   - What problems does it solve?
   - Who benefits from this agent?

2. **Why was it built?**
   - What need or pain point does it address?
   - What's the value proposition?
   - Why this approach vs alternatives?

3. **How will it be used?**
   - What are typical usage scenarios?
   - Who interacts with it (users, other systems)?
   - What triggers or activates it?

### Step 2: Integration & Agent Relationships

**CRITICAL**: Ask explicit questions about how this agent integrates with other agents and systems.

1. **Agent-to-agent interactions**:
   - Does this agent interact with other agents?
   - Which agents does it work with or depend on?
   - What does it receive from other agents?
   - What does it provide to other agents?

2. **Shared resources**:
   - Does it share memory or knowledge base with other agents?
   - Are there common data stores or resources?
   - How is shared information synchronized?

3. **Workflow integration**:
   - Does this agent trigger other agents?
   - Is it triggered by other agents?
   - What's the sequence of agent interactions?
   - Are there handoffs between agents?

4. **Dependencies**:
   - What agents must exist for this one to work?
   - What happens if dependent agents are unavailable?
   - Are there version dependencies between agents?

5. **Communication patterns**:
   - How do agents communicate (commands, shared memory, APIs)?
   - What protocols or conventions are used?
   - Are there message formats or standards?

### Step 3: Agent Structure & Organization

Gather information about how the agent is organized:

1. **Repository structure**:
   - Important directories and their purposes
   - Where specifications live
   - Where memory/knowledge is stored
   - Command organization

2. **Agent capabilities**:
   - What custom commands does it have?
   - What knowledge domains does it cover?
   - What workflows does it automate?

3. **Agent development workflow**:
   - How are new features added (specifications)?
   - How are commands created?
   - How is the agent tested/validated?
   - How is documentation maintained?

### Step 3: Discover the "Why" Behind Decisions

**CRITICAL**: CLAUDE.md should include just the right amount of "why" - enough to help Claude make good decisions without overwhelming with business rationale.

Ask questions to uncover:

1. **Architectural decisions**:
   - Why was this structure chosen?
   - What constraints influenced design?
   - What trade-offs were made?

2. **Key design choices**:
   - Why these specific capabilities?
   - What requirements drove these choices?
   - What problems do they solve?

3. **Important constraints**:
   - Scope boundaries and why
   - Security/privacy considerations and why
   - User experience priorities and why
   - Integration requirements and why

**Example of right amount of "why"**:
```markdown
## Agent Responses
Always provide concise, actionable responses. Avoid verbose explanations unless requested.

**Why:** This agent serves busy professionals who need quick answers.
Users have indicated they prefer brevity and can ask for details when needed.
```

**NOT this (too much PRD-style "why")**:
```markdown
After 6 months of user interviews across 3 market segments...
[extensive business justification]
```

### Step 4: Agent Conventions

Ask about:

1. **Documentation style**:
   - Specification format
   - Command documentation standards
   - Memory structure conventions

2. **Git workflow**:
   - Branching strategy
   - Commit message format
   - PR process for agent updates

3. **Validation approach**:
   - How agent behaviors are tested
   - Quality standards
   - Review process

4. **Command standards**:
   - Naming conventions for commands
   - Command structure patterns
   - Parameter conventions

## CLAUDE.md Creation

### Structure Template

Once you have gathered sufficient information, create the CLAUDE.md file with this structure:

```markdown
# [Agent Name]

[2-3 sentence overview: what this agent does, why it exists, and what value it provides]

## Purpose & Context

### What This Agent Does
[Clear description of the agent's primary function]

### Why It Exists
[The problem it solves and the value it provides - technical/architectural rationale, not extensive business justification]

### Key Design Decisions
[Important architectural or technical choices and their rationale]
- **Decision**: [What was chosen]
  **Why**: [Reasoning - user needs, scope, constraints, etc.]

## Repository Structure

```
[Show the directory layout]
agent-name/
├── .claude/
│   └── commands/          # Custom Claude Code commands for this agent
├── specifications/        # Feature and command specs (natural language)
├── memory/               # Agent knowledge base and persistent context
└── CLAUDE.md            # This file - Claude Code's operational guide
```

### Key Directories
- **`.claude/commands/`**: [Purpose and what's here - these are natural language instruction files]
- **`specifications/`**: [Purpose and what's here - natural language documents]
- **`memory/`**: [Purpose and what's here - knowledge base in natural language]
- **[Other important directories]**: [Purpose]

## Agent Capabilities

### Core Functions
[What this agent can do - its main capabilities]

### Custom Commands
[Brief overview of available `.claude/commands/` - they'll be documented more fully elsewhere]
- `/command-name`: [Brief purpose]

### Knowledge Domains
[What areas this agent has expertise in, what it knows about]

## Agent Development Workflow

### Creating Specifications
[How to define new agent features or behaviors]
```
Example: Use /create_agent_feature to document new capabilities
```

### Adding Commands
[How to create new Claude Code commands for the agent]
```
Example: Use /create_agent_command to specify new workflows
```

### Updating Agent Knowledge
[How to add to or modify the agent's knowledge base/memory]

### Git Workflow
- **Branching**: [Strategy and conventions]
- **Commits**: [Message format and standards]
- **Pull Requests**: [Process and requirements]

## Agent Conventions

### Documentation Standards
- [Specification format]
- [Command documentation style]
- [Memory structure patterns]

### Command Naming
- [Naming conventions for commands]
- [Parameter patterns]

### Response Style
- [How the agent should communicate]
- [Tone and format preferences]

### What to Avoid
- [Behaviors to prevent]
- [Out of scope activities]
- [Constraints and boundaries]

## Agent-Specific Context

### Custom Commands
[Brief overview of available `.claude/commands/` - they'll be documented more fully elsewhere]

### Memory/Knowledge Base
[How the agent uses persistent memory, where it's stored, what it contains]

### Integration Points

#### Agent Relationships
[Which other agents this agent works with]
- **[Agent Name]**: [Nature of relationship - depends on, provides data to, triggers, etc.]
- **[Agent Name]**: [Nature of relationship]

#### Communication Patterns
[How this agent communicates with other agents]
- **Method**: [Commands, shared memory, APIs, etc.]
- **Protocols**: [Any conventions or standards used]
- **Data formats**: [How information is exchanged]

#### Shared Resources
[Resources shared with other agents]
- **Shared memory**: [Location and what's shared]
- **Common knowledge**: [Shared knowledge bases]
- **Dependencies**: [What this agent requires from others]

#### Workflow Sequences
[How this agent fits into larger multi-agent workflows]
- **Triggers**: [What causes this agent to activate]
- **Outputs**: [What it produces for downstream agents]
- **Handoffs**: [When/how control passes to other agents]

## Important Constraints

[List critical constraints with their reasoning]

- **[Constraint]**: [Why this matters - user needs, scope, etc.]
- **[Constraint]**: [Why this matters - user needs, scope, etc.]

## Key Files & Locations

### Configuration
- **[Config file/location]**: [What it controls]

### Documentation
- **[Doc location]**: [What's documented there]

### Knowledge Base
- **[Memory location]**: [What's stored there]

## Agent Development Guidelines

### Validation
- [How to test/validate agent behaviors]
- [Quality standards]

### Documentation
- [When to update docs]
- [Documentation standards]

### Review
- [What reviewers look for]
- [Quality standards]

## Troubleshooting

### Common Issues
- **[Issue]**: [Solution or where to look]

### Debugging Agent Behavior
- [How to diagnose issues]
- [Useful techniques]
```

## Asking Questions to Determine "Why"

**CRITICAL INSTRUCTION**: When gathering requirements, actively ask questions to determine the right amount of "why" context to include.

Use these question patterns:

1. **For design decisions**:
   - "Why did you choose this approach over alternatives?"
   - "What requirements or constraints led to this design?"
   - "What problem does this solve for your users?"

2. **For conventions and constraints**:
   - "Why is this convention important for this agent?"
   - "What happens if this rule isn't followed?"
   - "What's the user impact of this decision?"

3. **For capability choices**:
   - "What need does this serve?"
   - "Why is this priority important?"
   - "What trade-offs were made here?"

### Determining "Just Right" Amount of Why

Include "why" when it helps Claude:
- ✅ Make better decisions in similar situations
- ✅ Understand constraints and trade-offs
- ✅ Apply principles to new scenarios
- ✅ Prioritize correctly when faced with choices

Exclude "why" when it's:
- ❌ Extensive business justification or market research
- ❌ Detailed user interview findings
- ❌ Historical context that doesn't inform current work
- ❌ Stakeholder politics or organizational dynamics

## File Location

Save the CLAUDE.md file to the repository root:
```
[repository-path]/CLAUDE.md
```

## Validation & Review

After creating the file, ask the user:

```
I've created a CLAUDE.md file with:
- Clear overview of what this agent does and why it exists
- Repository structure and key directories
- Agent capabilities and custom commands
- Agent development workflow and conventions
- The right amount of "why" context to help Claude make good decisions

Quick check:
- Does this capture the essential context Claude needs?
- Is there enough "why" for good decision-making without excessive business detail?
- Any missing conventions or important constraints?
- Does the structure make sense for your agent?

Would you like me to adjust anything or add more detail to specific sections?
```

## Quality Checklist

Before finalizing the CLAUDE.md:

- [ ] Clear, concise overview of agent's purpose
- [ ] Repository structure clearly documented
- [ ] Agent capabilities and commands specified
- [ ] Agent development workflow provided
- [ ] Conventions and constraints listed
- [ ] Key decisions include "why" rationale (not excessive business justification)
- [ ] "Why" context is actionable for Claude's decision-making
- [ ] Agent-specific infrastructure noted (commands, memory, specs)
- [ ] Emphasizes this is a natural language codebase (no traditional code)
- [ ] File is concise and scannable (not overwhelming)
- [ ] Follows "written for AI" principle (operational, not narrative)

## Best Practices

### Keep It Lean
- CLAUDE.md consumes token budget - prioritize essential information
- Use bullet points over paragraphs
- Be declarative and direct
- Remove redundancy

### Focus on Operations
- Emphasize "how to work with this agent"
- Include reasoning, not business justification
- Provide context that helps Claude make decisions
- Skip extensive narrative or historical background

### Make It Living
- CLAUDE.md should evolve with the agent
- Update when conventions change
- Add learnings from usage
- Refine based on what helps vs confuses

### Write for AI
- You're instructing an AI, not onboarding a human
- Be explicit about what Claude should and shouldn't do
- Include constraints and boundaries clearly
- Provide examples when helpful

### Remember: This is Natural Language Programming
- The agent is defined through natural language, not code
- Commands are instruction files, not scripts
- Specifications are documents, not technical specs
- The codebase is entirely text-based instructions

## Next Steps After Creation

Once CLAUDE.md is created, remind the user:

```
Your CLAUDE.md is ready! This will help Claude Code understand how to work effectively in your agent repository.

Next steps you might consider:
1. Commit this to version control so your team benefits from consistent AI behavior
2. Use `/create_agent_command` to define custom commands for your agent
3. Create feature specifications in `specifications/` using `/create_agent_feature`
4. Set up your agent's memory/knowledge base structure

The CLAUDE.md is a living document - update it as your agent evolves and you learn what helps Claude work better!

Would you like help with any of these next steps?
```
