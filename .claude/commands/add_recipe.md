# Add Recipe

## Overview
Automate the capture and standardization of recipes from various sources. Paste raw recipe text, and this command will parse it, link ingredients to component files, calculate nutritional information from those components, and create a standardized markdown file ready for meal planning and shopping lists.

**Template**: Always use `templates/recipe.md` as the formatting reference. The output recipe must follow this template structure exactly.

## Purpose & Value

### Workflow Being Automated
Collecting recipes from books, websites, and other sources currently requires manual data entry and formatting. This command extracts recipe information from pasted text, standardizes the format using the recipe template, links ingredients to component files, and automatically calculates nutritional information per serving from the linked components—eliminating manual formatting inconsistencies.

### Time/Effort Savings
- Consistent formatting across all recipes (via template)
- Automatic ingredient parsing, unit conversion, and component linking
- Nutrition calculated automatically from component files
- Ready-to-use recipes for meal planning and shopping lists immediately

### Target Users
- Anyone building a personal recipe collection
- Users creating meal plans and shopping lists from recipes
- Cooks who want nutritional information without manual research

## Command Invocation

### Command Name
```
/add_recipe
```

### Parameters
None - the command is fully interactive.

### Usage Examples
```bash
# Invoke the command
/add_recipe

# Then paste your full recipe text when prompted
```

## Procedural Requirements

### Prerequisites
- `memory/recipes/` directory exists (recipes stored in `memory/recipes/[recipe-name].md`)
- `memory/components/` directory exists with component files containing nutritional data
- `templates/recipe.md` exists as the formatting reference

### Step-by-Step Workflow

#### Step 1: Initialize & Verify Prerequisites
**Purpose**: Ensure directories exist and template is available.

**Actions**:
1. Verify `memory/recipes/` directory exists; create if needed
2. Verify `memory/components/` directory exists with subdirectories (proteins, vegetables, starches, sauces, fruits, dairy)
3. Read `templates/recipe.md` to confirm template structure
4. Display greeting and instructions

**Validation**:
- Directories are writable
- Template file exists and is readable

**Error Handling**:
- If directories don't exist: Create them automatically
- If template missing: Error and instruct user to create `templates/recipe.md`

**Important Notes**:
- **IMPORTANT**: Always read `templates/recipe.md` first to ensure output follows the exact template structure.

---

#### Step 2: Collect Recipe Text
**Purpose**: Get the raw recipe text from the user.

**Actions**:
1. Prompt user: "Paste the full recipe text (include sections marked with 'Ingredients:' and 'Instructions:', plus any title, story, or intro). When done, press Enter twice:"
2. Accept multi-line input until user signals completion (double Enter)
3. Store raw text for parsing

**Validation**:
- Text contains at least "Ingredients" and "Instructions" sections
- Text is not empty

**Error Handling**:
- If sections not found: Prompt user to ensure recipe includes clearly marked "Ingredients:" and "Instructions:" sections
- If empty: Ask user to paste recipe and try again

**Important Notes**:
- **IMPORTANT**: Look for section headers like "Ingredients:", "Instructions:" to identify different parts of the recipe. Be flexible with formatting variations.

---

#### Step 3: Parse Recipe Text
**Purpose**: Extract title, story/intro, ingredients, and instructions from raw text.

**Actions**:
1. Extract title (usually first line or look for common patterns)
2. Extract story/intro (text before "Ingredients:" section)
3. Extract ingredients list (between "Ingredients:" and "Instructions:" markers)
4. Extract instructions (after "Instructions:" marker)
5. Attempt to extract metadata if present:
   - Servings (look for "serves X" or "yield")
   - Prep time (look for "prep time", "preparation time")
   - Cook time (look for "cook time", "cooking time")

**Validation**:
- All major sections (ingredients, instructions) successfully extracted
- Title is present or ask user to provide one
- Ingredient list is non-empty

**Error Handling**:
- If title not found: Prompt "What is the recipe title?"
- If servings not extracted: Will prompt in Step 5
- If prep/cook times not extracted: Will prompt in Step 6
- If ingredients section unclear: Flag and ask for clarification

**Important Notes**:
- **IMPORTANT**: Extract ingredients word-for-word from the source as much as possible. Preserve original ingredient names and descriptions.
- Handle variations in formatting: some recipes use bullets, some use numbered lists, some use prose.

---

#### Step 4: Parse Ingredient Quantities
**Purpose**: Extract quantities and units from each ingredient for component matching and nutrition calculation.

**Actions**:
1. For each ingredient extracted:
   a. Parse ingredient name and amount (e.g., "2 cups flour" → name: "flour", amount: 2, unit: "cups")
   b. If amount uses non-metric units (cups, tablespoons, etc.):
      - Convert to grams using standard conversion factors
      - **FLAG the conversion** to user (e.g., "Converting '2 cups flour' to 256g")
   c. Store parsed quantity for nutrition calculation

2. Handle vague quantities:
   - "a pinch", "to taste" → Flag and ask for approximate amount or mark as negligible
   - "1 large", "2 medium" → Use standard sizes (large egg = 50g, medium onion = 110g, etc.)

**Validation**:
- Amounts are clear and convertible to grams
- All ingredients have parseable quantities

**Error Handling**:
- If amount is vague (e.g., "a pinch", "to taste"): Flag and ask "Please specify amount for '{ingredient}' (e.g., 1 teaspoon, 100g):" or offer to mark as negligible
- If conversion uncertain: Ask user to confirm (e.g., "I'm converting '2 cups flour' to ~256g. Is this correct? (yes/no)")

**Important Notes**:
- **IMPORTANT**: Flag all unit conversions so user can verify accuracy.
- Handle ingredient variations: "flour" might be "all-purpose flour", "butter" might be "salted butter", etc.

---

#### Step 4.5: Check for Missing Components
**Purpose**: Identify which ingredients don't have existing component files and prompt user to create them before proceeding.

**Actions**:
1. List all existing components by scanning `memory/components/`:
   - `proteins/` - list all `.md` files
   - `vegetables/` - list all `.md` files
   - `starches/` - list all `.md` files
   - `fruits/` - list all `.md` files
   - `sauces/` - list all `.md` files
   - `dairy/` - list all `.md` files

2. For each ingredient from Step 3, determine if it's:
   - A **significant ingredient** (protein, vegetable, starch, fruit, dairy) that SHOULD have a component
   - A **pantry staple** (oil, salt, pepper, spices, water, vinegar, etc.) that does NOT need a component

3. For significant ingredients, search for a matching component file:
   - Match by name (e.g., "salmon fillets" → `salmon-fillets.md`)
   - Match by partial name (e.g., "baby spinach" → `spinach.md`)
   - Be flexible with naming variations

4. Display a summary to the user:
   ```
   Component Check:

   ✓ Found components:
     - salmon fillets → proteins/salmon-fillets.md
     - baby potatoes → starches/baby-potatoes.md

   ✗ Missing components (need to be created):
     - Greek yogurt (dairy)
     - English cucumber (vegetable)
     - avocado (fruit)

   ○ Pantry staples (no component needed):
     - olive oil
     - kosher salt
     - black pepper
   ```

5. If missing components exist, ask user:
   ```
   Would you like to:
   (1) Create the missing components now (I'll guide you through each one)
   (2) Continue without these components (they won't be linked, nutrition will be incomplete)
   (3) Stop and create components manually first
   ```

6. If user chooses (1):
   - For each missing component, prompt for:
     - Category confirmation (e.g., "Is 'Greek yogurt' a protein or dairy? [proteins/dairy]:")
     - Supermarket link (optional)
     - Serving size
     - Nutrition info per serving (calories, protein, carbs, fat, fiber at minimum)
   - Create component file using `templates/component.md`
   - Confirm creation: "Created: memory/components/dairy/greek-yogurt.md"

7. If user chooses (2):
   - Mark those ingredients as unlinked in the recipe
   - Add a note in the recipe: `*(component not yet created)*`
   - Warn that nutrition will be incomplete

8. If user chooses (3):
   - Pause the workflow
   - Display instructions for manual component creation
   - User can re-run `/add_recipe` after creating components

**Validation**:
- All significant ingredients are accounted for (either matched, created, or explicitly skipped)
- User has made an explicit choice for each missing component
- New components follow `templates/component.md` format

**Error Handling**:
- If component directory missing: Create it
- If user input unclear: Re-prompt with clearer options
- If component creation fails: Report error and offer to skip that component

**Important Notes**:
- **IMPORTANT**: This step ensures all significant ingredients have components BEFORE linking, making the linking step straightforward.
- **IMPORTANT**: Always show the user what's missing and let them decide how to proceed.
- **IMPORTANT**: Components must have nutrition data for the recipe nutrition calculation to work.

**Pantry Staples (no component needed):**
The following common items should be automatically skipped - they don't need component files:
- **Oils & Fats**: olive oil, avocado oil, vegetable oil, coconut oil, sesame oil, butter
- **Seasonings**: salt, kosher salt, sea salt, black pepper, white pepper
- **Aromatics**: garlic, ginger, shallots, green onion/scallion tops
- **Vinegars & Acids**: vinegar (all types), lemon juice, lime juice
- **Sauces & Condiments**: soy sauce, fish sauce, coconut aminos, worcestershire sauce, hot sauce
- **Sweeteners**: sugar, honey, maple syrup, brown sugar
- **Herbs & Spices**: all dried herbs and spices (oregano, cumin, paprika, etc.)
- **Fresh Herbs**: cilantro, parsley, basil, dill, mint, thyme, rosemary (used as garnish/flavoring)
- **Liquids**: water, stock/broth (unless homemade), wine (cooking)
- **Thickeners**: flour (when used for thickening), cornstarch
- **Misc**: baking powder, baking soda, vanilla extract

If unsure whether an ingredient is a pantry staple, ask the user.

---

#### Step 4.6: Link Ingredients to Components
**Purpose**: Connect each ingredient to its corresponding component file for nutritional tracking and shopping list generation.

**Actions**:
1. For each significant ingredient (verified in Step 4.5):
   - Link using markdown: `[ingredient name](../components/[category]/[component-name].md)`
   - Example: `[salmon fillets](../components/proteins/salmon-fillets.md)`

2. For pantry staples:
   - Leave unlinked with note: `*(no component - pantry staple)*`

3. For ingredients where component was skipped:
   - Leave unlinked with note: `*(component not yet created)*`

**Validation**:
- All significant ingredients with existing components are linked
- Links use correct relative paths: `../components/[category]/[name].md`
- Pantry staples and skipped ingredients are clearly marked

**Important Notes**:
- **IMPORTANT**: Component links enable nutritional tracking across recipes and automated shopping list generation.
- **IMPORTANT**: Relative paths must be correct: from `memory/recipes/[recipe].md` to `../components/[category]/[component].md`

---

#### Step 5: Calculate Nutrition from Components
**Purpose**: Sum nutritional content from linked component files and calculate per-serving values.

**Actions**:
1. For each linked component:
   a. Read the component file from `memory/components/[category]/[name].md`
   b. Extract nutrition data per serving and serving size
   c. Calculate nutrition for the amount used in the recipe:
      - If recipe uses 200g and component serving is 100g, multiply nutrition by 2
      - Handle unit conversions as needed

2. Sum all nutritional values across all linked components:
   - Calories, protein, carbs, fat (total), saturated fat, fiber
   - Sodium, potassium, calcium, iron, magnesium (if available)
   - Vitamins A, C, D (if available)

3. Extract or prompt for servings count:
   - If servings was extracted in Step 3: Use that value
   - Otherwise: Prompt "How many servings does this recipe make?"

4. Divide all totals by servings count

5. Round all values to 2 significant figures

**Validation**:
- Servings count is a positive integer
- All nutritional values calculated and divided correctly
- Components with missing nutrition data are flagged

**Error Handling**:
- If servings is 0 or invalid: Prompt "Please enter valid servings (positive number):"
- If component missing nutrition data: Warn user and note in recipe that nutrition is incomplete
- If ingredient has no component: Skip in nutrition calculation, note as incomplete

**Important Notes**:
- **IMPORTANT**: All nutritional values in final output are PER SERVING, not total.
- **IMPORTANT**: Nutrition accuracy depends on component files having complete data.
- If some components lack nutrition data, note this in the recipe's nutrition section.

---

#### Step 6: Collect Scaling & Substitution Preferences
**Purpose**: Determine how the recipe scales for multiple servings (e.g., dinner + lunch leftovers) and adjust ingredient quantities to match supermarket packaging.

**Actions**:
1. Ask user: "How would you like to scale this recipe for multiple occasions (e.g., dinner + lunch the next day)? For example, '2x for dinner + lunch leftovers'. [optional, press Enter to skip]:"
   - User may respond with:
     - A multiplier: "2x" (double the recipe)
     - Specific occasion mapping: "1.5x for dinner + next day lunch"
     - Skip (Enter) if no scaling needed

2. If scaling provided:
   - Recalculate all ingredient quantities by the scaling factor
   - Recalculate servings count accordingly
   - Update the recipe's serving size description to reflect the scaling intent (e.g., "Serves 6 (3 dinner + 3 lunch leftovers)")

3. Ask user: "Do you want to adjust ingredient quantities to match supermarket packaging? (e.g., salmon fillets come in 4-pack at 110g each) [yes/no, optional]:"
   - If yes: For each ingredient, offer:
     - "For [ingredient name]: Current amount is [X]. Supermarket option: [option]. Use supermarket quantity? (yes/no):"
     - Allow user to input custom supermarket packaging options
     - Update ingredient quantities based on available packaging

4. Store final adjusted quantities and scaling rationale

**Validation**:
- Scaling factor is numeric and > 0 if provided
- Adjusted quantities are realistic (not absurdly large or small)
- Scaling rationale is recorded for future reference

**Error Handling**:
- If scaling factor not numeric: Re-prompt "Please enter scaling factor (e.g., 1.5, 2, 0.5):"
- If adjustment results in unrealistic quantities: Warn user and ask to confirm

**Important Notes**:
- **IMPORTANT**: This step helps recipes be more practical for real-world shopping and meal planning. Users often need to scale recipes to their household size and work with supermarket packaging constraints.
- Store the original recipe quantities for reference, and note the scaling applied.

---

#### Step 6.5: Collect Missing Metadata
**Purpose**: Gather any metadata not extracted from the recipe text.

**Actions**:
1. For each metadata field, check if it was extracted in Step 3:
   - Servings (already confirmed in Step 5 and possibly adjusted in Step 6)
   - Prep time (minutes, e.g., "15 min")
   - Cook time (minutes, e.g., "30 min")
   - Difficulty (1-5 scale: 1=very easy, 5=very hard)
   - Cuisine type (e.g., "Italian", "Asian", "American", etc.)
   - Dietary restrictions (comma-separated: e.g., "vegan, gluten-free")
   - Tags/categories (comma-separated: e.g., "dessert, quick, weeknight")
   - Source attribution (original source: book title, website URL, etc.)

2. For missing fields, prompt user:
   - "Prep time (minutes)? [optional, press Enter to skip]:"
   - "Cook time (minutes)? [optional, press Enter to skip]:"
   - "Difficulty (1-5)? [optional, press Enter to skip]:"
   - "Cuisine type? [optional, press Enter to skip]:"
   - "Dietary restrictions? [optional, comma-separated, press Enter to skip]:"
   - "Tags/categories? [optional, comma-separated, press Enter to skip]:"
   - "Source attribution? [optional, press Enter to skip]:"

3. Store all collected metadata

**Validation**:
- Difficulty is 1-5 if provided
- Times are numeric if provided
- All other fields are strings

**Error Handling**:
- If difficulty not 1-5: Re-prompt "Please enter difficulty 1-5:"
- If times not numeric: Re-prompt "Please enter time in minutes (number):"
- If invalid input: Ask user to re-enter

---

#### Step 7: Create Standardized Markdown File
**Purpose**: Generate the final recipe markdown file following `templates/recipe.md` exactly, including component links, scaling, and supermarket packaging adjustments.

**Actions**:
1. Read `templates/recipe.md` to get the exact section structure and formatting
2. Generate filename: Convert recipe title to lowercase, replace spaces with hyphens, remove special characters
   - Example: "Chocolate Chip Cookies" → "chocolate-chip-cookies.md"
3. Create markdown file at `memory/recipes/[filename]` following the template structure:
   - **Title** (# header)
   - **Overview** (story/intro, optional but recommended)
   - **Scaling Notes** (optional, if scaling was applied in Step 6)
   - **Supermarket Packaging Notes** (optional, if adjustments made)
   - **Metadata** (table format: Servings, Prep Time, Cook Time, Total Time, Difficulty, Cuisine, Dietary, Tags, Source)
   - **Ingredients** (with component links and optional grouping)
   - **Instructions** (numbered list with optional grouping)
   - **Nutrition Information** (per serving, table format - calculated from components)
   - **Storage & Meal Prep Notes** (optional, for meal-prep-friendly recipes)
   - **Notes** (tips, pairings, modifications)
   - **Footer** (Source, Adapted for, Date Added)

4. Format details (must match template exactly):
   - Title: `# Recipe Title`
   - Overview: Plain text paragraph(s)
   - Metadata: Table format with Field | Value columns
   - Ingredients with component links:
     ```markdown
     - 6 [salmon fillets](../components/proteins/salmon-fillets.md) (660g total)
     - 500g [baby potatoes](../components/starches/baby-potatoes.md), halved
     - 2 tablespoons olive oil *(no component - pantry staple)*
     ```
   - Scaling Notes (if applicable):
     ```markdown
     **Scaling Applied**: 2x for dinner + lunch leftovers
     **Original Servings**: 2 | **Adjusted Servings**: 4
     **Ingredient Adjustments**:
     - Salmon fillets scaled to 4-pack (440g total at 110g each)
     ```
   - Instructions: Numbered steps, grouped by phase if complex
   - Nutrition: Table format with Nutrient | Per Serving columns

5. Write file to disk

**Validation**:
- File created successfully at `memory/recipes/[filename]`
- File structure matches `templates/recipe.md` exactly
- All significant ingredients have component links (or marked as pantry staples)
- Component link paths are correct: `../components/[category]/[name].md`
- Scaling notes are clear and complete if applied
- Markdown is properly formatted
- Nutrition calculated from linked components

**Error Handling**:
- If filename collision: Append timestamp or number (e.g., "chocolate-chip-cookies-2.md")
- If write fails: Report error and ask user to check directory permissions

**Important Notes**:
- **IMPORTANT**: Output must follow `templates/recipe.md` structure exactly. Read the template before generating output.
- **IMPORTANT**: All significant ingredients must link to components using relative paths: `../components/[category]/[name].md`
- All nutritional values should be clearly labeled with units (g, mg, IU, µg).
- Include flagged unit conversions as comments so user can verify.
- Scaling notes help future meal planning: if you use this recipe again, you'll see the scaling approach and supermarket constraints that informed the original amounts.
- If any components lacked nutrition data, add a note: "* Nutrition incomplete - some components missing data"

---

#### Step 8: Confirmation & Output
**Purpose**: Confirm success and show user where recipe is stored.

**Actions**:
1. Display success message with file location
2. Show brief summary of what was created
3. List any new components created
4. Note if nutrition is incomplete due to missing component data
5. Provide next steps

**Output Format**:
```
✓ Recipe added successfully!

File: memory/recipes/chocolate-chip-cookies.md
Template used: templates/recipe.md

Recipe Summary:
- Title: Chocolate Chip Cookies
- Servings: 24 cookies
- Prep Time: 15 minutes
- Cook Time: 12 minutes
- Difficulty: 2/5
- Cuisine: American
- Tags: dessert, quick, weeknight
- Scaling Applied: No
- Components Linked: 5 (flour, butter, eggs, chocolate chips, sugar)
- New Components Created: 0
- Nutrition: Complete (calculated from components)

The recipe is ready to use for meal planning and shopping lists!
```

**Alternative Output (with Scaling and New Components)**:
```
✓ Recipe added successfully!

File: memory/recipes/salmon-bowl.md
Template used: templates/recipe.md

Recipe Summary:
- Title: Almond-Quinoa & Salmon Bowl
- Servings: 4 (2 dinner + 2 lunch leftovers)
- Prep Time: 20 minutes
- Cook Time: 25 minutes
- Difficulty: 2/5
- Cuisine: Fusion
- Tags: lunch, dinner, quick, protein-heavy
- Scaling Applied: 2x for dinner + lunch leftovers
- Supermarket Adjustments: Salmon fillets scaled to 4-pack (440g total at 110g each)
- Components Linked: 6 (salmon, quinoa, almonds, spinach, avocado, lemon)
- New Components Created: 1 (memory/components/proteins/almonds.md)
- Nutrition: Complete (calculated from components)

The recipe is ready to use for meal planning and shopping lists!
See the "Scaling Notes" section in the recipe for details on adjustments made.
```

---

## File Structure & Paths

### Files Read
```
templates/
└── recipe.md              # Template for recipe formatting (MUST be read first)

memory/
└── components/            # Read for ingredient linking AND nutrition data
    ├── proteins/
    ├── vegetables/
    ├── starches/
    ├── fruits/
    ├── dairy/
    └── sauces/
```

### Files Created
```
memory/
└── recipes/
    ├── chocolate-chip-cookies.md
    ├── pasta-carbonara.md
    └── [recipe-name].md

memory/
└── components/            # New components created if needed
    └── [category]/
        └── [component-name].md
```

### Path Conventions
- **Template path**: `templates/recipe.md` (read at start of workflow)
- **Recipe output**: `memory/recipes/[recipe-name].md`
- **Component links in recipes**: Relative paths from recipe to component: `../components/[category]/[name].md`
- **Recipe filenames**: Lowercase, hyphens instead of spaces, no special characters

## Validation & Testing

### Success Criteria
- [ ] Template `templates/recipe.md` is read before generating output
- [ ] Recipe text is parsed correctly (title, ingredients, instructions, metadata extracted)
- [ ] All significant ingredients linked to components (or marked as pantry staples)
- [ ] Component links use correct relative paths: `../components/[category]/[name].md`
- [ ] Nutritional values calculated from component files and divided by servings correctly
- [ ] Markdown file created at `memory/recipes/[recipe-name].md`
- [ ] File structure matches `templates/recipe.md` exactly
- [ ] All nutritional values shown per serving with 2 significant figures
- [ ] Unit conversions flagged for user verification
- [ ] User receives clear confirmation with file location and components linked

## Integration Notes

### Related Workflows
- **Meal Planning**: Use recipes from `memory/recipes/` to create weekly meal plans
- **Shopping Lists**: Extract ingredients from selected recipes (via component links) to generate shopping lists
- **Recipe Search**: Query recipes by tags, cuisine type, dietary restrictions, or difficulty
- **Nutritional Tracking**: Component links enable aggregating nutrition across recipes

### Notes for Implementation
- This command should handle raw text input flexibly, accommodating variations in recipe formatting from different sources
- Nutrition is calculated by reading component files - no external API needed
- Interactive prompts should be clear and help users provide clarifications when needed
- All user input should be validated before proceeding to next steps

## Troubleshooting & Common Issues

### Missing Component Nutrition Data

**Issue**: "Nutrition incomplete - component missing data"
- **Cause**: A linked component file doesn't have complete nutrition information
- **Solution**: Edit the component file at `memory/components/[category]/[name].md` to add nutrition data
- **Format**: Ensure the component has a Nutrition section with at minimum: Calories, Protein, Carbs, Fat

**Issue**: "Cannot calculate nutrition for ingredient X"
- **Cause**: No component exists for this ingredient
- **Solution**: Create a component file for the ingredient, or mark it as a pantry staple if it's negligible nutritionally

### Component Matching Issues

**Issue**: "Ingredient not matched to component"
- **Cause**: Component file name doesn't match ingredient name
- **Solution**: Either rename the component file or manually specify the match during the workflow
- **Tip**: Component files use kebab-case (e.g., `chicken-breast.md`), recipe ingredients may use different naming

**Issue**: "Multiple possible component matches"
- **Solution**: The workflow will ask you to confirm which component to use

### Accuracy & Data Quality

**Issue**: "Nutritional values seem off"
- **Cause**: Component files may have incorrect data, or serving sizes don't match what's used in the recipe
- **Solution**:
  1. Check the component file's serving size matches what you're using
  2. Verify the component's nutrition data is accurate
  3. Manually edit the recipe's nutrition table if needed after creation

**Issue**: "I want to override the calculated nutrition"
- **Solution**: After the recipe is created, open the markdown file and edit the nutrition table directly
- **Format**: Keep the same table structure; values should be per serving
