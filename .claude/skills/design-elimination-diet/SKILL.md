---
name: design-elimination-diet
description: >
  **Elimination Diet Planner**: Designs structured elimination diets to test suspected food triggers, with specific meal plans that remove target ingredients while maintaining nutritional balance and variety. Use this skill when the user wants to test whether a food is triggering symptoms, design a FODMAP elimination phase, plan a reintroduction protocol, create an elimination diet, or structure a food sensitivity test. Also trigger when the user says "I want to cut out X and see what happens", "elimination diet", "FODMAP elimination", "reintroduction plan", "test if X is a trigger", or discusses removing specific foods to test their impact on symptoms.
---

# Design Elimination Diet

You help the user design structured elimination diets — removing suspected trigger ingredients while maintaining nutritional balance, variety, and practical cookability. Elimination diets are hypothesis tests: remove a variable, observe the result, reintroduce to confirm.

## Where to Find Things

- **FODMAP plan**: `memory/fodmap_plan.md` — Existing elimination/reintroduction protocol, safe foods list, known tolerances
- **Recipes**: `memory/recipes/*.md` — Available meals and their full ingredient lists
- **Components**: `memory/components/` — Individual food items the user knows how to cook
- **Previous meal plans**: `memory/weeks/*/meals.md` — What the user actually eats (realistic basis for modifications)
- **Symptom log**: `memory/symptom_log.md` — Current symptom data to inform which ingredients to target
- **Symptom analysis**: Output from the analyze-symptoms skill, if previously run

## Workflow

### 1. Identify the Target

Work with the user to determine what they're eliminating. This might be:
- A specific ingredient (e.g., garlic, onions, mushrooms)
- A FODMAP category (e.g., fructans, polyols)
- A food group (e.g., nightshades, dairy)
- Multiple suspects from symptom analysis

If the user isn't sure what to eliminate, review their symptom log and FODMAP plan to suggest candidates based on correlation data.

### 2. Assess Impact on Current Meals

Read through the user's recipe database and recent meal plans to identify:
- Which recipes contain the eliminated ingredient(s)
- Which recipes are already safe (no modifications needed)
- Which recipes can be modified with simple substitutions
- Whether there's enough variety remaining for a full week of meals

Present this as a clear impact assessment so the user understands what changes.

### 3. Design Substitutions

For each affected recipe, propose specific modifications:
- What replaces the eliminated ingredient (both for flavor and nutrition)
- Whether the modification is tested/proven or experimental
- How it affects the recipe's overall nutrition

Use the FODMAP plan's substitution table if available (e.g., garlic-infused oil instead of garlic, green spring onion tops instead of onion).

### 4. Create the Elimination Phase Plan

Design a concrete meal plan for the elimination period:

**Duration**: Typically 2-6 weeks for FODMAP elimination, 2-3 weeks for single ingredient tests.

**Meal plan**: 1-2 weeks of specific meals using safe recipes and modified versions. The user can rotate these for the full elimination period.

**Nutritional check**: Verify the elimination plan still meets nutritional needs. Flag if removing an ingredient creates a significant gap (e.g., removing legumes reduces fiber and plant protein).

**Monitoring protocol**: What to track during elimination — Bristol scale, eczema, ears, energy, mood. Remind the user to log consistently in `symptom_log.md`.

**Success criteria**: What improvement looks like (roughly 50-75% symptom reduction for FODMAP elimination).

### 5. Design the Reintroduction Protocol

After the elimination period (assuming symptoms improve), design a structured reintroduction:

**Testing order**: Based on FODMAP reintroduction best practices or the specific food being tested.

**Challenge format**:
- Day 1: Small amount (1/4 serving)
- Day 2: Medium amount (1/2 serving)
- Day 3: Full serving
- Days 4-6: Rest period (strict elimination diet)
- Day 7+: Next challenge or conclude

**What to track**: Symptoms within 24 hours of each challenge day, using the enhanced tracking format from `fodmap_plan.md`.

**Decision framework**: How to interpret results (Pass / Moderate / Fail) and what each means for long-term diet.

### 6. Output Files

Every elimination plan must include both the elimination phase AND the reintroduction protocol. An elimination without a structured reintroduction is incomplete — the whole point is to confirm triggers through systematic re-exposure.

Create or update these files as appropriate:
- Modified recipes in `memory/recipes/` (with "-elimination" suffix or similar)
- A concrete elimination phase meal plan in `memory/weeks/[MMDDYY]/meals.md`
- A reintroduction schedule with specific challenge foods, dosing protocol, and rest periods
- Updated `memory/fodmap_plan.md` with the testing plan and timeline

## Key Principles

- **One variable at a time.** The whole point of elimination testing is isolating variables. Don't change multiple things simultaneously unless the user is doing a broad elimination phase (like full low-FODMAP).
- **Maintain variety and nutrition.** An elimination diet that's so restrictive it's unsustainable won't produce useful results. Keep meals interesting and nutritionally complete.
- **Use the user's existing recipes.** Don't invent entirely new meals — modify what they already know and enjoy. This makes compliance much more likely.
- **Set clear timelines.** Elimination diets are diagnostic tools, not permanent restrictions. Always define an end date and reintroduction plan.
- **Be rigorous about tracking.** The value of elimination testing depends entirely on consistent symptom logging. Remind the user of this.
- **No medical claims.** You're helping design a structured self-experiment, not prescribing treatment. Recommend consulting a FODMAP-trained dietitian for complex cases.
