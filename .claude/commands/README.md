# Meal Planner Agent Commands

This directory contains custom Claude Code commands that automate workflows for the Meal Planning Agent. Each command is a specialized tool designed to help with different aspects of meal planning, nutrition analysis, and digestive health tracking.

## Quick Start

Commands are invoked using the `/` prefix in Claude Code. For example:

```
/plan_weekly_meals
/add_recipe
```

---

## Available Commands

### 📋 `/plan_weekly_meals`
**Purpose:** Automate weekly meal planning with nutritional analysis and shopping lists

Plan your week by selecting dinners from your recipe database. This command generates:
- A complete meal plan with day-by-day breakdown
- Automated lunch planning (leftovers from dinner)
- Consolidated shopping list organized by category
- Nutritional summary with macros and micros
- Recipe links for easy reference

**When to use:**
- You want to plan your meals for the week
- You need a shopping list from your selected meals
- You want to see nutritional totals for your planned week

**Workflow:**
1. Provide the Sunday date for the week
2. Select how many days you'll be cooking (1-5)
3. Choose your dinner meals
4. Agent generates the complete plan and shopping list

[Full command specification →](plan_weekly_meals.md)

---

### 🍳 `/add_recipe`
**Purpose:** Add new recipes to your knowledge base with automated nutritional analysis

Convert recipes from any source into a standardized format with full nutritional data. The command:
- Parses recipe text and extracts ingredients
- Looks up nutritional information for each ingredient
- Calculates nutrition per serving
- Allows scaling for meal prep scenarios (dinner + lunch leftovers)
- Adjusts quantities for supermarket packaging
- Creates a standardized markdown file

**When to use:**
- You discover a new recipe you want to track
- You want nutritional data without manual research
- You need consistent recipe formatting across your library
- You're scaling recipes for multiple occasions (dinner + lunches)

**Workflow:**
1. Paste the recipe text (include title, ingredients, instructions)
2. Confirm or provide missing metadata (servings, prep time, etc.)
3. Specify scaling if needed (e.g., 2x for dinner + lunch leftovers)
4. Adjust quantities for supermarket packaging
5. Agent creates the formatted recipe file

[Full command specification →](add_recipe.md)

---

### 🔧 `/create_agent_command`
**Purpose:** Design and build new custom commands for this agent

This meta-command helps you create new workflow automation commands. It guides you through:
- Discovering the workflow you want to automate
- Defining step-by-step procedures
- Planning error handling and validation
- Creating a complete command specification

**When to use:**
- You identify a repetitive workflow that needs automation
- You want to add a new custom command to extend the agent
- You need to document a complex process

**Workflow:**
1. Describe the workflow or pain point
2. Discuss automation approach and design
3. Define step-by-step procedures
4. Review and refine the command specification

[Full command specification →](create_agent_command.md)

---

### 🏗️ `/create_claude_md`
**Purpose:** Generate a CLAUDE.md system prompt for this or another agent

This command helps you create or update the CLAUDE.md file that serves as the agent's operational guide. It covers:
- Agent purpose and design decisions
- Repository structure and conventions
- Agent capabilities and workflows
- Integration with other agents (if applicable)
- Development guidelines and constraints

**When to use:**
- Setting up a new agent repository
- Documenting major updates to the agent's design
- Onboarding Claude Code to work effectively in your agent

**Workflow:**
1. Describe the agent's purpose and scope
2. Document the repository structure
3. Define conventions and constraints
4. Specify integration points with other systems
5. Agent creates the complete CLAUDE.md file

[Full command specification →](create_claude_md.md)

---

## Command Patterns & Conventions

### Naming
- Commands use lowercase with underscores: `/command_name`
- Names are verb-noun or verb-focused: `/plan_weekly_meals`, `/add_recipe`

### Invocation
- Commands are fully interactive—no required parameters
- You provide input through prompts and conversation
- Commands validate input and provide clear error messages

### Output
- Commands create files and show confirmation messages
- Generated files are ready to use immediately
- Paths and locations are clearly displayed

---

## Command Development Workflow

### Creating New Commands

1. **Identify the workflow**: What manual process needs automation?
2. **Plan the command**: Use `/create_agent_command` to specify it
3. **Implement**: Create the command file in this directory
4. **Test**: Invoke the command and verify it works as expected
5. **Document**: Update this README with the new command

### Command Structure

Each command file includes:
- **Overview**: What the command does and why
- **Purpose & Value**: Workflow automation benefits
- **Command Invocation**: How to call it and with what parameters
- **Procedural Requirements**: Step-by-step workflow with validation
- **Error Handling**: What could go wrong and how to recover
- **File Structure & Paths**: What the command creates or modifies

---

## Related Resources

### System Instructions
📖 **[Full System Prompt](../CLAUDE.md)** — How the agent works, design decisions, conventions, and key workflows

### Recipe Knowledge Base
🍽️ **[Recipe Library](../memory/recipes/)** — Your curated collection of recipes with full nutritional data

### Weekly Planning
📅 **[Weekly Plans](../memory/weeks/)** — Historical meal plans, nutrition summaries, and shopping lists

### Health Tracking
📊 **[Symptom Log](../memory/symptom_log.md)** — Bristol scale entries for digestive health analysis

---

## Tips & Best Practices

### Getting the Most from Commands

**For `/plan_weekly_meals`:**
- Build up your recipe library first (15-20 recipes for good variety)
- Use consistent recipe names when selecting meals
- Review the nutritional summary to ensure balance

**For `/add_recipe`:**
- Include detailed ingredient lists (easier to parse)
- Note servings, prep time, and cook time in your input
- Specify scaling if the recipe is designed for multiple occasions

**For Custom Workflows:**
- Use `/create_agent_command` to document before building
- Keep commands focused on one workflow (easier to maintain)
- Include examples of input and output for clarity

---

## FAQ

**Q: Can I modify a command after it's created?**
A: Yes! Edit the markdown file directly. The command will use your updated version next time you invoke it.

**Q: What if a command doesn't do what I need?**
A: Use `/create_agent_command` to design a new command, or modify an existing one to better fit your workflow.

**Q: How do commands interact with the recipe database?**
A: Commands read from and write to `memory/recipes/` and `memory/weeks/`. Keep your recipes well-organized for best results.

**Q: Can I use these commands from other agents?**
A: These commands are specific to the Meal Planning Agent, but you could create similar commands in other agents following the same pattern.

---

## Getting Help

For detailed information about any command, read its full specification file:
- [plan_weekly_meals.md](plan_weekly_meals.md) — Full planning workflow guide
- [add_recipe.md](add_recipe.md) — Recipe addition and formatting guide
- [create_agent_command.md](create_agent_command.md) — Command creation meta-guide
- [create_claude_md.md](create_claude_md.md) — System prompt generation guide

Or check the [main README](../README.md) for the overall agent dashboard and quick links.
