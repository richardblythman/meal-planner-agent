---
name: add-recipe
description: >
  **Recipe Formatter & Adder**: Takes a recipe from any source (URL, photo, description, cookbook reference) and creates a properly formatted, nutrition-calculated recipe file in the knowledge base. Also creates any missing component files for ingredients. Use this skill whenever the user wants to add a new recipe, save a recipe they found, format a recipe for the database, log a meal they cooked, create a recipe from ingredients, or build out their recipe collection. Also trigger when the user shares a recipe link, photo of a recipe, or describes a meal they want to add. Trigger for "add recipe", "save this recipe", "I made X tonight and want to save it", "format this recipe", "new recipe", or when the user shares food-related URLs or images.
---

# Add Recipe

You take a recipe from any source and produce a properly formatted recipe file that fits the user's meal planning knowledge base. This includes calculating nutrition, adjusting for the user's serving needs, linking ingredients to component files, and creating any missing components.

## Where to Find Things

- **Existing recipes**: `memory/recipes/*.md` — Reference for format and style conventions
- **Recipe template**: `templates/recipe.md` — The canonical template for new recipes
- **Component template**: `templates/component.md` — Template for new ingredient components
- **Existing components**: `memory/components/{proteins,vegetables,starches,fruits,dairy}/*.md` — Check what already exists
- **FODMAP plan**: `memory/fodmap_plan.md` — Flag any high-FODMAP ingredients

## Input Sources

The user might provide a recipe as:
- A **URL** to a recipe website
- A **photo** of a recipe page or cookbook
- A **verbal description** ("I made salmon with rice and broccoli, here's roughly what I did...")
- A **cookbook reference** ("It's the ginger peanut noodle recipe from Half Baked Harvest")
- **Raw ingredients and steps** they want formatted

Accept whatever format they give you and work with it.

## Workflow

### 1. Extract Recipe Information

From whatever source, extract:
- Recipe name
- Ingredients with quantities
- Instructions/steps
- Serving count
- Prep and cook times
- Source attribution

If information is incomplete, ask the user to fill gaps. Nutrition data you'll calculate yourself.

### 2. Adjust for the User's Needs

The user typically scales recipes for **dinner + lunch leftovers** (usually 4-8 servings for 2 people across 2 meals). Ask if they want to scale, and adjust quantities accordingly.

The user also has **different portion sizes** from their partner, especially for protein. Check existing recipes like `memory/recipes/blackened-chicken-taco-bowls.md` to see how portion splits are documented.

**Supermarket packaging**: The user shops at Tesco (Ireland). When possible, note practical package sizes. For example, if a recipe calls for 500g chicken, note "705g from Tesco pack — you'll have 205g spare" or adjust the recipe to use the full pack.

### 3. Calculate Nutrition

Calculate nutrition per serving using the component files where available and your nutritional knowledge for everything else. Include at minimum:
- Calories, Protein, Carbs, Fat (Total), Saturated Fat, Fiber

For recipes with separate portion sizes (user vs. partner), calculate both.

If the recipe uses ingredients that have component files, pull nutrition from those files for consistency. For ingredients without components, use standard nutritional databases (USDA, etc.) and note the source.

Include additional micronutrients when they're notable for the recipe — for example, omega-3s for fish recipes, iron for red meat, vitamin C for recipes heavy on peppers/broccoli.

### 4. Link to Components

Ingredients should link to their component files using relative paths:
```markdown
- 660g [salmon fillets](../components/proteins/salmon-fillets.md)
- 500g [baby potatoes](../components/starches/baby-potatoes.md)
```

Common pantry staples (oils, salt, spices, garlic, ginger, vinegar, soy sauce, honey, etc.) don't need component links — mark them with `*(pantry staple)*`.

### 5. Create Missing Components

If a recipe uses an ingredient that should have a component file but doesn't (significant proteins, vegetables, starches, fruits, dairy), create it:

1. Use `templates/component.md` as the format reference
2. Include: Category, Unit Size (as purchased), Serving Size, nutrition per serving, store link if known
3. Save to the appropriate subdirectory in `memory/components/`

Only create components for ingredients that are reusable across recipes. One-off specialty items or pantry staples don't need components.

### 6. Check for FODMAP Concerns

This step is non-optional — the user has an active FODMAP investigation and needs every recipe flagged.

Read `memory/fodmap_plan.md`. Cross-reference every ingredient in the new recipe against the FODMAP plan's "Foods to AVOID" and "Vegetable Serving Size Quick Reference" sections. Common culprits that are easy to miss: garlic (fructans), onions of any type (fructans), mushrooms (mannitol), sugar snap peas (GOS/fructans at >16g), avocado (sorbitol at >30g), black beans (GOS).

Add a clearly labeled "FODMAP Notes" subsection in the recipe's Notes section:
- Which ingredients are high-FODMAP and which FODMAP category they belong to
- Suggested low-FODMAP substitutions from the FODMAP plan's substitution table
- Safe serving sizes if the ingredient is borderline (reference the serving size table in fodmap_plan.md)

### 7. Write the Recipe File

Save to `memory/recipes/[recipe-name-kebab-case].md` following the template structure. Key sections:

- **Overview**: Brief description of the recipe
- **Scaling Notes**: If scaled from original, document the adjustment
- **Metadata table**: Servings, times, difficulty, cuisine, dietary tags, source
- **Ingredients**: Grouped by component (e.g., "For the Sauce", "For the Chicken"), with component links
- **Instructions**: Numbered steps, optionally grouped by phase
- **Nutrition per Serving**: Table with macros and relevant micros, separate tables if portions differ
- **Storage & Meal Prep Notes**: How leftovers keep, reheating tips, prep-ahead opportunities
- **Notes**: Cooking tips, variations, pairing suggestions

### 8. Confirm with the User

After creating the file, give the user a brief summary:
- Recipe name and servings
- Key nutrition highlights
- Any FODMAP flags
- Any new components created
- Where the file was saved

## Quality Standards

- **Nutrition accuracy matters.** The user relies on these numbers for weekly analysis. Double-check calculations, especially when scaling. Show your math for anything non-obvious.
- **Consistency with existing recipes.** Look at 2-3 existing recipes in `memory/recipes/` before writing to match the user's style (how they handle sections, how detailed the notes are, etc.)
- **Practical package sizes.** The user shops at Tesco Ireland. When you know the typical package size, note it in scaling notes or ingredients.
- **The template is the authority.** Follow `templates/recipe.md` for structure. Don't invent new sections or skip required ones.
