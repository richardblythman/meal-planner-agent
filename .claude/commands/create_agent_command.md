# Create Agent Command

You are tasked with discovering, defining, and creating comprehensive Claude Code commands through collaborative conversation and iterative requirements gathering. Your role is to guide the user through command workflow ideation, understanding procedural requirements, and producing a complete command file.

## Initial Response

When this command is invoked:

1. **Check if parameters were provided**:
   - If a command idea, workflow description, context, or rough outline was provided as a parameter, begin the discovery process with that context
   - If files are referenced, read them FULLY first to understand existing context
   - If no parameters provided, respond with the default prompt below

2. **If no parameters provided**, respond with:
```
I'll help you discover, define, and specify a new Claude Code command through collaborative conversation. Let's start by understanding what workflow you want to automate.

What command or workflow are you considering? This could be:
- A repetitive task you want to automate ("initialize new projects with...")
- A multi-step workflow that's error-prone ("scaffold feature directories...")
- A complex process that needs consistency ("deploy agents to...")
- An integration between tools ("sync data with...")

Don't worry about having all the details - we'll explore, refine, and create the command together!

Tip: You can also invoke this command with context: `/create_agent_command scaffold feature workflow` or `/create_agent_command based on docs/workflows/deploy_process.md`
```

Then wait for the user's input.

## Process Overview

This command combines workflow discovery and command creation into a unified workflow:

1. **Discovery Phase**: Collaborative exploration to understand the workflow and define the command
2. **Requirements Phase**: Detailed command creation with step-by-step procedures and validation
3. **Command Creation**: Create the command file directly in `.claude/commands/[command-name].md`

## Phase 1: Discovery & Workflow Understanding

### Step 1: Context Gathering

1. **Read all referenced files immediately and FULLY**:
   - Related workflow documents or process descriptions
   - Existing commands for similar workflows
   - Technical documentation about tools or systems involved
   - Use the Read tool WITHOUT limit/offset parameters to read entire files
   - **CRITICAL**: DO NOT spawn sub-tasks before reading these files yourself
   - **NEVER** read files partially - if a file is mentioned, read it completely

2. **Review existing commands** (if relevant):
   - Check `.claude/commands/` for similar patterns and existing commands
   - Identify reusable procedural patterns
   - Note naming conventions and style

### Step 2: Workflow Exploration

1. **Understand the core workflow**:
   - What manual process needs automation?
   - Who will invoke this command?
   - What triggers the need for this workflow?
   - Any time/effort savings expected?

2. **Deep dive into the process**:
   - What are the current pain points?
   - How is this done manually today?
   - What makes the current approach error-prone or inefficient?
   - What prerequisites or setup are required?

3. **Acknowledge and probe deeper**:
   ```
   I understand you're thinking about automating [summarize their workflow].

   Let me ask some clarifying questions:
   - Walk me through the exact steps you currently take manually
   - What typically goes wrong or requires extra attention?
   - Are there variations of this workflow for different scenarios?
   - What inputs or decisions are needed along the way?
   ```

### Step 3: Solution Design & Scope Definition

1. **Explore automation approach**:
   - What steps can be fully automated vs. need user input?
   - Are there decision points that require clarification?
   - What validation or error checking is critical?
   - How should the command handle failures?

2. **Define the command behavior**:
   - What's the minimum viable command?
   - What parameters or inputs are needed?
   - How will users discover and invoke this?
   - What feedback should the command provide?

3. **Consider implementation details**:
   - What tools or CLIs are required (gh, git, but, etc.)?
   - What file system operations are needed?
   - What absolute paths vs relative paths?
   - What platform-specific considerations?

### Step 4: Command Naming & Invocation

1. **Choose command name**:
   - Use verb-noun format (e.g., `init_agent`, `scaffold_feature`, `deploy_agent`)
   - Keep it concise but descriptive
   - Check for conflicts with existing commands
   - Ensure it clearly describes the action

2. **Define invocation patterns**:
   ```
   How should users invoke this command?
   - `/command-name` (no parameters, interactive prompts)
   - `/command-name param1 param2` (with inline parameters)
   - `/command-name --option value` (with flags/options)

   Which pattern fits best for your workflow?
   ```

## Phase 2: Requirements Definition

### Step 1: Procedural Steps

1. **Break down the workflow**:
   - List each discrete step in order
   - Identify dependencies between steps
   - Note validation points
   - Highlight critical operations

2. **Define each step precisely**:
   ```
   For each step, we need to specify:
   - What exact operation to perform
   - What tools or commands to use
   - What paths (absolute vs relative)
   - What validation or error checking
   - What output to provide to the user
   ```

### Step 2: Parameters & Inputs

1. **Define what inputs are needed**:
   - Required parameters
   - Optional parameters with defaults
   - Interactive prompts for user decisions
   - File paths or external resources

2. **Specify input validation**:
   - What constitutes valid input?
   - What error messages for invalid input?
   - Any constraints or limitations?

### Step 3: Error Handling & Edge Cases

1. **Identify failure scenarios**:
   - What could go wrong at each step?
   - How to detect failures?
   - How to recover or rollback?
   - What error messages to show?

2. **Define validation checkpoints**:
   - Pre-conditions before starting
   - Validation after each critical step
   - Final verification of success
   - Cleanup on failure

## Phase 3: Command File Creation

### Step 1: Gather Metadata

1. **Collect command metadata**:
   - Command name and purpose
   - Author and creation date
   - Related commands or workflows
   - Version or status information

### Step 2: Create Command File

Write the command file directly using this template:

```markdown
# [Command Name]

## Overview
[2-3 sentence description of what this command automates and why it's valuable]

## Purpose & Value

### Workflow Being Automated
[Detailed description of the manual process this command replaces]

### Time/Effort Savings
- [How this improves efficiency]
- [Error reduction benefits]
- [Consistency improvements]

### Target Users
- [Who will use this command]
- [When they'll need it]
- [How often it's invoked]

## Command Invocation

### Command Name
```
/[command-name]
```

### Parameters
- `param1`: [Description, required/optional, default value]
- `param2`: [Description, required/optional, default value]

### Usage Examples
```bash
# Basic invocation
/[command-name]

# With parameters
/[command-name] param1 value1

# With options
/[command-name] --option value
```

## Procedural Requirements

### Prerequisites
[What must be in place before this command can run:]
- [Required tools (e.g., gh CLI, git, etc.)]
- [Required permissions or authentication]
- [Required directory structure or files]
- [Environment variables or configuration]

### Step-by-Step Workflow

#### Step 1: [Step Name]
**Purpose**: [What this step accomplishes]

**Actions**:
1. [Specific operation to perform]
2. [Command or tool to use with exact syntax]
3. [Parameters and paths (specify absolute vs relative)]

**Validation**:
- [How to verify this step succeeded]
- [What to check before proceeding]

**Error Handling**:
- [What could go wrong]
- [How to detect failure]
- [What error message to show]
- [Whether to continue or abort]

**Important Notes**:
- **IMPORTANT:** [Any critical warnings or gotchas]
- [Platform-specific considerations]
- [Path handling requirements]

---

#### Step 2: [Step Name]
[Repeat structure for each step]

---

#### Step N: Confirmation & Output
**Purpose**: Confirm success and provide feedback to user

**Actions**:
1. Display completion message
2. Show output information (paths, URLs, etc.)
3. Remind user of next steps or related commands

**Output Format**:
```
[Example of what the user should see on success]
```

## File Structure & Paths

### Files Created
```
[Show the directory structure this command creates or modifies:]

example-structure/
├── folder1/
│   ├── file1.ext
│   └── file2.ext
└── folder2/
    └── file3.ext
```

### Path Conventions
- **Absolute paths**: [When to use and examples]
- **Relative paths**: [When to use and examples]
- **User home directory**: [How to reference]
- **Project root**: [How to determine and use]

## Validation & Testing

### Success Criteria
- [ ] [Specific outcome that indicates success]
- [ ] [Files/directories created in correct locations]
- [ ] [Tools/services properly configured]
- [ ] [User receives clear confirmation]

## Integration Notes

### Related Commands
- `/related-command-1`: [How it relates and when to use]
- `/related-command-2`: [How it relates and when to use]

### Step 3: Save Command & Review

1. **Save command file to**:
   - `.claude/commands/[command-name].md`
   - Creates the command that can be invoked with `/[command-name]`

2. **Ask for focused feedback**:
   ```
   Command created at: .claude/commands/[command-name].md

   This command includes:
   - Complete workflow automation instructions
   - Step-by-step procedural guidance
   - Error handling and validation requirements
   - Testing and integration guidance

   Quick check:
   - Does this accurately capture the workflow you want to automate?
   - Any missing steps or edge cases?
   - Are the error scenarios covered?
   - Ready to test the command or need adjustments?
   ```

3. **Next steps guidance**:
   ```
   Your command is ready to use! Next steps:

   1. Test the command:
      - Try invoking `/[command-name]`
      - Verify each test case works as expected
      - Confirm error handling works as designed
      - Check output matches expectations

   2. Document in CLAUDE.md:
      - Add the command to the Available Commands section
      - Include usage examples and description

   Would you like me to help test the command or update CLAUDE.md?
   ```

## Conversation Guidelines

### Be Conversational & Collaborative
- Ask open-ended questions to understand the workflow
- Build on the user's process knowledge
- Use "How do you currently..." framing to understand existing workflows
- Acknowledge complexity and explore edge cases together
- Ask for user feedback often! Process design is iterative

### Probe for Procedural Detail
- Ask "What happens next?" to map the complete workflow
- Explore failure scenarios: "What if this step fails?"
- Identify decision points: "When do you choose between options?"
- Uncover implicit knowledge: "What do you just know to do?"

### Stay Automation-Focused
- Always bring conversation back to what can be automated
- Distinguish between steps requiring user input vs. fully automated
- Question overly complex workflows: "Could we simplify this?"
- Explore how users would discover and trust the automation

### Consider Command Design
- How does this fit with existing commands?
- Does this follow command naming conventions?
- What's the right level of abstraction?
- Should this be one command or multiple?

## Quality Checklist

Before finalizing the command:

- [ ] Clear workflow description that explains the automation value
- [ ] Complete step-by-step procedural requirements
- [ ] All parameters and inputs defined with validation rules
- [ ] Error scenarios identified with handling strategies
- [ ] Success criteria and test cases specified
- [ ] File paths and tool requirements documented
- [ ] Platform-specific considerations noted
- [ ] Integration with related commands described
- [ ] Document follows template structure with proper metadata

## Common Patterns for Agent Commands

### Repository Initialization
- Use `gh repo create` with appropriate flags
- Always specify `--add-readme` to establish HEAD
- Clone to specific directory locations
- Initialize additional tools (git-butler, etc.)

### File Structure Creation
- Use absolute paths for cross-directory operations
- Add `.gitkeep` files to track empty directories
- Commit and push structural changes
- Validate directory creation before proceeding

### Tool Integration
- Verify tool availability before use
- Provide clear error messages for missing tools
- Document required tool versions
- Handle authentication requirements

### User Interaction
- Offer choices before making decisions
- Confirm destructive operations
- Provide clear success/failure feedback
- Suggest next steps after completion

## Guidelines for Success

### Focus on Reliability
- Cover normal workflow and common error cases
- Include validation after critical steps
- Provide rollback or recovery mechanisms
- Give clear error messages with actionable guidance

### Be Precise
- Specify exact commands with full syntax
- Include all required flags and parameters
- Document absolute vs relative path requirements
- Reference actual file paths and tool names

### Document Thoroughly
- Explain the "why" behind each step
- Note platform-specific behaviors
- Identify prerequisites clearly
- Provide usage examples

### Plan for Implementation
- Structure the command clearly and logically
- Group related operations together
- Make decision points explicit
- Include testing guidance
