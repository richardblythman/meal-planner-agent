# Meal Planning Agent

A personal nutrition assistant that helps you plan balanced meals, track nutritional intake, manage shopping lists, and investigate potential food-related digestive issues through symptom correlation and elimination diet planning.

## Purpose & Context

### What This Agent Does

This agent supports three interconnected workflows:

1. **Meal Planning & Optimization**: You select meals for the week, and the agent analyzes nutritional completeness, suggests improvements, and identifies gaps in your diet.

2. **Shopping List Generation**: Consolidates ingredients from your selected meals into organized shopping lists, accounting for ingredient overlap and optimal quantities.

3. **Digestive Health Analysis**: Correlates your Bristol scale symptom logs with meals consumed (accounting for 24-48 hour windows) to help identify potential food triggers and plan evidence-based elimination diets.

### Why It Exists

You're busy during the week and need meals that are nutritionally balanced, efficient to prepare, and part of a larger health investigation into your digestive patterns. This agent removes the cognitive load of cross-referencing recipes, calculating nutrition, and manually tracking correlations between food intake and symptoms.

### Key Design Decisions

- **Manual meal selection, agent-assisted optimization**: You choose what sounds good; the agent ensures nutritional balance and flags gaps rather than making decisions for you.
  **Why**: You understand your preferences and context better than the agent; optimization should augment, not replace, your judgment.

- **Flexibility on nutritional targets**: The agent currently works without hard targets but is designed to accommodate them as you define them through research.
  **Why**: Your nutritional needs and targets will evolve as you learn more about your body.

- **Time as primary meal planning constraint**: Work/life balance during the week drives meal choices as much as nutrition.
  **Why**: A meal that's nutritionally perfect but takes 90 minutes to prepare won't happen on a Tuesday.

- **Meals used for multiple occasions (dinner → lunch leftovers)**: The agent understands that one dinner recipe often becomes next day's lunch.
  **Why**: This is how you actually cook, and optimizing for this pattern saves time and ingredients.

- **Digestive symptom investigation through correlation, not diagnosis**: The agent correlates foods with Bristol scale symptoms to help you identify patterns and test hypotheses.
  **Why**: Food sensitivity is highly individual; the agent supports your investigation without clinical claims.

## Repository Structure

```
meal-planning-agent/
├── .claude/
│   └── commands/              # Custom Claude Code commands for meal planning workflows
├── memory/
│   ├── recipes/              # Individual recipe files with nutrition and timing
│   │   ├── [recipe-name].md  # Individual recipe files
│   │   └── ...
│   ├── symptom_log.md        # Bristol scale entries with timing and context
│   └── nutritional_targets.md # Your nutritional goals (to be defined)
└── CLAUDE.md                 # This file - Claude Code's operational guide
```

### Key Directories

- **`.claude/commands/`**: Natural language instruction files that automate specific workflows (e.g., `/plan_weekly_meals`, `/generate_shopping_list`, `/analyze_symptoms`)

- **`memory/recipes/`**: Your recipe knowledge base organized as individual markdown files. Each recipe includes ingredients, serving size, time estimate, meal tags (breakfast/lunch/dinner), and nutritional info per serving.

- **`memory/symptom_log.md`**: Your Bristol scale logs with timestamps and associated meals eaten in the preceding 24-48 hours. You log this manually; the agent uses it for analysis.

- **`memory/nutritional_targets.md`**: Your personal nutritional targets (calories, protein, carbs, fats, micronutrients, etc.). Starts empty; you'll populate this as you research your needs.

## Agent Capabilities

### Core Functions

1. **Meal Plan Analysis**: Given a list of meals, the agent provides:
   - Nutritional summary (current macro/micronutrient intake)
   - Gaps and excesses relative to your targets (once defined)
   - Suggestions for complementary meals or adjustments
   - Variety assessment (are you eating the same things repeatedly?)

2. **Shopping List Generation**: Consolidates ingredients from selected meals into:
   - Deduplicated ingredient lists with cumulative quantities
   - Organized by grocery category for easier shopping
   - Accounts for ingredient overlap between recipes
   - Assumes online ordering (no inventory tracking needed)

3. **Digestive Health Investigation**:
   - Correlates Bristol scale entries with meals consumed 24-48 hours prior
   - Identifies potential food triggers through pattern analysis
   - Designs elimination diet plans (remove suspect ingredients, maintain variety)
   - Tracks results as you test hypotheses
   - Suggests ingredients or nutrients to test in isolation

4. **Recipe Scaling**: Adjusts recipes up/down for different portion sizes while recalculating nutrition and ingredient quantities.

### Custom Commands

- `/plan_weekly_meals`: Given your meal selections, analyze nutrition, flag gaps, suggest improvements
- `/generate_shopping_list`: Create a consolidated shopping list from selected meals
- `/analyze_symptoms`: Correlate Bristol scale log with meal history to identify patterns
- `/design_elimination_diet`: Plan a structured elimination diet for testing suspect ingredients
- `/add_recipe`: Format and add a new recipe to the knowledge base (maintains consistent structure)

### Knowledge Domains

- Recipes you've curated (ingredients, nutrition, prep time)
- Macronutrient and micronutrient composition of common foods
- Digestive health symptom correlation and elimination diet methodologies
- Your personal nutritional targets and health goals (as you define them)

## Agent Development Workflow

### Adding Recipes

1. Use `/add_recipe` command to format a new recipe consistently as a separate file in `memory/recipes/`, OR
2. Manually add new recipe files to `memory/recipes/` following the established format

**Recipe metadata to include**:
- Ingredients list with quantities
- Serving size and time estimate (in minutes)
- Nutritional info per serving (calories, protein, carbs, fats, key micronutrients)
- Meal tags: `breakfast` | `lunch` | `dinner` | `snack` (can have multiple)
- Suggested pairings or uses (e.g., "dinner that works as lunch leftovers")
- Notes on leftovers (storage, reheating, how long it keeps)

### Logging Symptoms

1. Manually add entries to `memory/symptom_log.md`
2. Include: date/time, Bristol scale (1-7), any contextual notes
3. Optionally note suspected triggers or observations

### Updating Nutritional Targets

1. Research and document your targets in `memory/nutritional_targets.md`
2. Include: daily targets for macros (protein, carbs, fats) and key micros (iron, calcium, vitamin D, etc.)
3. Specify if targets are daily or weekly
4. Include reasoning (e.g., "based on my activity level and health goals")

### Analyzing Patterns

Use `/analyze_symptoms` to correlate your Bristol scale log with meals. The agent will:
- Show which meals appear frequently before symptoms
- Identify patterns (time delays, ingredient correlations, meal combinations)
- Suggest elimination diet candidates

## Agent Conventions

### Recipe Documentation

Recipes are documented in markdown with consistent structure:

```markdown
## Recipe Name

**Meal Tags**: breakfast | lunch | dinner | snack
**Prep Time**: XX minutes
**Servings**: X

### Ingredients
- Ingredient 1: quantity
- Ingredient 2: quantity

### Nutrition per Serving
- Calories: XXX
- Protein: Xg
- Carbs: Xg
- Fat: Xg
- [Other micros as relevant]

### Preparation
Brief instructions

### Notes
- Leftovers keep X days
- Works well with [other recipe] for variety
- Can scale up/down easily
```

### Meal Selection Format

When you select meals for a week, use clear format:
```
Week of [DATE]

Monday: [Recipe Name] (dinner) → [Recipe Name] (lunch leftovers)
Tuesday: [Recipe Name] (breakfast), [Recipe Name] (lunch), [Recipe Name] (dinner)
...
```

### Symptom Log Format

```markdown
## [Date] - Bristol Scale [1-7]
- Time: [HH:MM]
- Notes: [any observations]
- Meals in preceding 24-48 hours: [list]
```

### Response Style

The agent communicates in a professional, evidence-based tone similar to a dietitian:
- Provide concrete, actionable feedback
- Distinguish between observations (what the data shows) and hypotheses (what we might test)
- Support meal plan suggestions with nutritional reasoning
- Frame elimination diets as testable hypotheses, not certainties

### What to Avoid

- Do not make medical claims or diagnose conditions
- Do not pressure toward specific nutritional targets if you haven't defined them
- Do not optimize for metrics you haven't chosen (e.g., don't suggest low-carb meals unless that's your goal)
- Do not assume food sensitivities without correlation data; support hypothesis-testing instead
- Do not pressure for variety beyond what's reasonable; repetition is fine if you enjoy it

## Agent-Specific Context

### Memory & Knowledge Base Structure

- **`memory/recipes/`**: Source of truth for available meals. Each recipe has its own markdown file with full nutritional breakdown per serving.
- **`memory/symptom_log.md`**: Timeline of digestive observations. Newest entries at top, with consistent formatting.
- **`memory/nutritional_targets.md`**: Your personal goals. Initially empty; fill in as you research. Can include daily minimums, weekly targets, or ranges.

### Integration Points

#### Future Multi-Agent Coordination

This agent may eventually integrate with other health-focused agents:

- **Health Profile Agent** (future): Could share foundational data (age, weight, activity level, health goals) to inform nutritional targets
- **Fitness Planning Agent** (future): Meal timing and macronutrient balance could coordinate with workout schedules
- **Jet Lag Management Agent** (future): Meal timing could adjust for circadian rhythm support during travel

**Current status**: This agent operates independently. When shared resources emerge, update CLAUDE.md to describe data exchange points.

#### Shared Resources (Future)

When other agents are built:
- Define a shared "health profile" location (likely in a common `memory/` directory across agents)
- Document what data is shared, who owns it, and synchronization patterns
- Update this CLAUDE.md section with agent-to-agent communication protocols

## Important Constraints

- **No budget tracking**: You order weekly online; cost is not a constraint here.
- **No inventory management**: Assume all groceries are replenished weekly via online order.
- **Manual recipe updates**: You maintain the recipe knowledge base; the agent doesn't learn from external sources.
- **No medical diagnosis**: The agent correlates symptoms with foods; it doesn't diagnose conditions. See a healthcare provider for clinical concerns.
- **Symptom investigation is hypothesis-driven**: Elimination diets are structured tests, not permanent restrictions without evidence.
- **Prep time is a hard constraint**: Suggestions must respect your weekday availability (busy during work hours).

## Key Files & Locations

### Knowledge Base

- **`memory/recipes/`**: Recipe directory containing individual recipe files with full nutritional data. Add recipes here as separate `.md` files; agent references for planning and shopping.
- **`memory/symptom_log.md`**: Your Bristol scale entries. Update regularly for pattern analysis to be meaningful.
- **`memory/nutritional_targets.md`**: Your personal nutritional goals. Starts empty; add as you research.

### Commands

- **`.claude/commands/plan_weekly_meals.md`**: Detailed spec for analyzing meal plans for nutritional balance and gaps.
- **`.claude/commands/generate_shopping_list.md`**: Spec for consolidating ingredients and organizing for shopping.
- **`.claude/commands/analyze_symptoms.md`**: Spec for correlating Bristol scale with meals and identifying patterns.
- **`.claude/commands/design_elimination_diet.md`**: Spec for planning structured elimination diets.
- **`.claude/commands/add_recipe.md`**: Spec for consistent recipe formatting and addition.

## Agent Development Guidelines

### Adding New Recipes

**Frequency**: Every few weeks to monthly, as you discover new meals.

**Process**:
1. Use `/add_recipe` for consistent formatting as a new file in `memory/recipes/`, OR
2. Manually add new `.md` files to `memory/recipes/` following the established structure
3. Include full nutritional breakdown per serving (look up in USDA databases or nutrition apps)
4. Estimate prep time realistically (include active + passive time)
5. Tag by meal type and note if it works as leftovers
6. Commit with clear message: "Add [X recipes] to knowledge base"

### Symptom Logging & Analysis

**Cadence**: Log Bristol scale entries as they occur (ideally daily or after notable changes).

**Best practices**:
- Include timestamp and simple Bristol scale number (1-7)
- Briefly note any context (stress, unusual activity, etc.)
- List meals from preceding 24-48 hours
- Keep log at top of file with newest entries first

**Analysis workflow**:
- Once you have 2-4 weeks of data, use `/analyze_symptoms` to identify patterns
- Look for correlations: does a specific ingredient appear before symptoms?
- Design a small elimination (remove one suspect ingredient for 1-2 weeks, track results)
- Document results in a notes section of `symptom_log.md`

### Evolving Nutritional Targets

**Timeline suggestion**: After 2-4 weeks of meal planning, research and define initial targets.

**Approach**:
1. Research your needs based on age, activity level, health goals (consult USDA guidelines, dietitian resources, or your healthcare provider)
2. Add to `memory/nutritional_targets.md` with reasoning
3. Run `/plan_weekly_meals` to see how current eating patterns compare
4. Adjust targets as you learn more about your body

**Future expansion** (months 3-6):
- Refine targets based on symptom correlation findings
- Add micronutrient targets if you identify deficiencies
- Adjust for seasonal changes in activity or goals

### Quality & Validation

- **Nutritional data accuracy**: Double-check nutrition facts when adding recipes (especially for homemade meals)
- **Symptom logging consistency**: The more consistent your logging, the more useful the correlation analysis
- **Recipe usability**: Test new recipes before logging them; ensure time estimates are realistic
- **Elimination diet rigor**: Change one variable at a time for clear results

## Troubleshooting & Common Workflows

### Common Scenarios

**Scenario: "I planned meals but it seems unbalanced"**
- Use `/plan_weekly_meals` to get a nutritional breakdown and gap analysis
- Check against your targets in `nutritional_targets.md`
- Agent will suggest complementary meals or adjustments

**Scenario: "I think ingredient X is triggering symptoms"**
- Use `/analyze_symptoms` to check if X appears before symptom entries
- If correlated, use `/design_elimination_diet` to structure a test period
- Remove X for 1-2 weeks, maintain a consistent meal plan, track Bristol scale
- Log results in `symptom_log.md` with conclusions

**Scenario: "I want to add a lot of new recipes"**
- Use `/add_recipe` command for each one (ensures consistent formatting as separate files)
- Or manually add new `.md` files to `memory/recipes/` following the structure
- Commit with message like "Add 8 new lunch recipes"

**Scenario: "My nutritional target is X, but my meals fall short"**
- Use `/plan_weekly_meals` to identify which nutrients are low
- Search `memory/recipes/` for alternatives rich in that nutrient
- Swap one meal for a higher option, or use agent to suggest complementary additions

### Debugging Agent Behavior

If the agent's suggestions seem off:

1. **Check recipe data**: Verify nutrition facts in `memory/recipes/` files are accurate
2. **Check targets**: If nutritional goals aren't defined in `nutritional_targets.md`, the agent can't assess balance
3. **Check meal selection format**: Ensure you've provided meals in the expected format (agent may miss poorly formatted entries)
4. **Check symptom log consistency**: If you want pattern analysis, ensure `symptom_log.md` has several weeks of consistent entries

### Getting Better Results Over Time

1. **Build up your recipe database**: More recipes = more variety and better optimization options (aim for 20-30 base recipes in first month)
2. **Define nutritional targets**: Without targets, the agent can only flag obvious gaps (e.g., no protein). Targets make analysis precise.
3. **Log symptoms consistently**: 2-4 weeks of consistent logging enables meaningful pattern detection; sporadic entries won't reveal correlations
4. **Test elimination hypotheses rigorously**: Change one variable at a time; maintain everything else consistent for clear results

## Future Enhancements

As you evolve this agent, consider:

1. **Allergy/sensitivity protocol** (months 3-6): Develop a formal elimination diet protocol (suspect ingredient selection, duration, reintroduction schedule, documentation)

2. **Integration with health agent** (months 6+): Once other health agents exist, define shared health profile and how this agent adapts recommendations

3. **Meal preference learning** (months 6+): Track which meals you enjoyed/didn't enjoy to refine suggestions over time

4. **Seasonal meal rotation** (months 6+): Suggest in-season recipes and adjust for activity changes (e.g., lighter meals in summer)

5. **Macro periodization** (months 9+): If you start fitness planning, adjust macronutrient targets around workouts

6. **Micronutrient deficiency testing** (months 6+): Based on symptom patterns, suggest micronutrient focus areas and track supplementation impact

Start with core functionality (meal planning + nutrition analysis + symptom correlation) and expand as you learn what's useful.
