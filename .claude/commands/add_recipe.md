# Add Recipe

## Overview
Automate the capture and standardization of recipes from various sources. Paste raw recipe text, and this command will parse it, automatically estimate nutritional content using USDA FoodData Central, and create a standardized markdown file ready for meal planning and shopping lists.

**Template**: Always use `templates/recipe.md` as the formatting reference. The output recipe must follow this template structure exactly.

## Purpose & Value

### Workflow Being Automated
Collecting recipes from books, websites, and other sources currently requires manual data entry and formatting. This command extracts recipe information from pasted text, standardizes the format using the recipe template, links ingredients to component files, and automatically calculates nutritional information per serving using USDA data—eliminating manual nutrition lookups and formatting inconsistencies.

### Time/Effort Savings
- Eliminate manual nutrition research and calculation
- Consistent formatting across all recipes (via template)
- Automatic ingredient parsing, unit conversion, and component linking
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
- USDA FoodData Central API key (free, get one at: https://fdc.nal.usda.gov/api-key-signup.html)
- API key stored as environment variable: `USDA_API_KEY`
  - **Option 1 (Recommended)**: Store in `.env` file at project root as `USDA_API_KEY=your_key_here` (will be automatically loaded)
  - **Option 2**: Export as environment variable: `export USDA_API_KEY="your_key_here"`
  - **Option 3**: Add to shell profile (`.bashrc`, `.zshrc`, etc.) for permanent availability
- `memory/recipes/` directory exists (recipes stored in `memory/recipes/[recipe-name].md`)
- `memory/components/` directory exists with component files for ingredient linking
- `templates/recipe.md` exists as the formatting reference

**Note on API Key Security**: Store your API key in `.env` (which should be in `.gitignore`) or as a shell environment variable, never commit it to version control.

### Step-by-Step Workflow

#### Step 1: Initialize & Verify Prerequisites
**Purpose**: Ensure API key is configured, directories exist, and template is available.

**Actions**:
1. Check if `USDA_API_KEY` environment variable is set
2. Verify `memory/recipes/` directory exists; create if needed
3. Verify `memory/components/` directory exists with subdirectories (proteins, vegetables, starches, sauces, fruits)
4. Read `templates/recipe.md` to confirm template structure
5. Display greeting and instructions

**Validation**:
- API key is accessible
- Directories are writable
- Template file exists and is readable

**Error Handling**:
- If API key not found: Prompt user with instructions to set `USDA_API_KEY` environment variable and re-run
- If directories don't exist: Create them automatically
- If template missing: Error and instruct user to create `templates/recipe.md`

**Important Notes**:
- **IMPORTANT**: The USDA API key must be set before proceeding. Store it as an environment variable for security.
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
- If prep/cook times not extracted: Will prompt in Step 5
- If ingredients section unclear: Flag and ask for clarification

**Important Notes**:
- **IMPORTANT**: Extract ingredients word-for-word from the source as much as possible. Preserve original ingredient names and descriptions.
- Handle variations in formatting: some recipes use bullets, some use numbered lists, some use prose.

---

#### Step 4: Normalize & Look Up Ingredients with USDA
**Purpose**: Convert ingredients to standard units and fetch nutritional data.

**Actions**:
1. For each ingredient extracted:
   a. Parse ingredient name and amount (e.g., "2 cups flour" → name: "flour", amount: 2, unit: "cups")
   b. If amount uses non-metric units (cups, tablespoons, etc.):
      - Convert to grams using standard conversion factors
      - **FLAG the conversion** to user (e.g., "Converting '2 cups flour' to 256g")
   c. Query USDA FoodData Central API using the ingredient name
   d. If exact match found: Record nutritional data for that amount
   e. If no match or multiple results: Prompt user to confirm which ingredient to use

2. Collect nutritional data per ingredient:
   - Calories, protein, carbs, fat (total), saturated fat, unsaturated fat, fiber
   - Major vitamins: A, B12, C, D, E, K
   - Major minerals: calcium, iron, sodium, potassium, magnesium
   - All values rounded to 2 significant figures

**Validation**:
- All ingredients successfully matched to USDA entries
- Amounts are clear and convertible to grams
- Nutritional data retrieved for each ingredient

**Error Handling**:
- If ingredient not found in USDA: Prompt "'{ingredient}' not found in USDA database. Please clarify (e.g., 'white flour', 'unsalted butter', etc.):"
- If amount is vague (e.g., "a pinch", "to taste"): Flag and ask "Please specify amount for '{ingredient}' (e.g., 1 teaspoon, 100g):"
- If conversion uncertain: Ask user to confirm (e.g., "I'm converting '2 cups flour' to ~256g. Is this correct? (yes/no)")
- **If USDA API is unreachable or returns errors**:
  - Log the error with timestamp and ingredient being looked up
  - Fall back to standard USDA nutrition reference data for that ingredient (pre-calculated values)
  - Notify user: "Note: Using reference nutrition data for '{ingredient}' (live API lookup unavailable)"
  - Continue processing without blocking the recipe creation
  - Provide disclaimer in recipe notes if fallback was used

**Important Notes**:
- **IMPORTANT**: Be thorough with ingredient matching. If the USDA database returns multiple results, show options to user.
- **IMPORTANT**: Flag all unit conversions so user can verify accuracy.
- Handle ingredient variations: "flour" might be "all-purpose flour", "butter" might be "salted butter", etc.
- **API Resilience**: The command should not fail entirely if the USDA API is temporarily unavailable; use cached/reference data instead.

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

2. For each ingredient from Step 3, determine if it's:
   - A **significant ingredient** (protein, vegetable, starch, fruit) that SHOULD have a component
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
   (2) Continue without these components (they won't be linked)
   (3) Stop and create components manually first
   ```

6. If user chooses (1):
   - For each missing component, prompt for:
     - Category confirmation (e.g., "Is 'Greek yogurt' a protein or dairy? [proteins/dairy]:")
     - Supermarket link (optional)
     - Serving size
     - Basic nutrition info (or offer to look up via USDA)
   - Create component file using `templates/component.md`
   - Confirm creation: "Created: memory/components/proteins/greek-yogurt.md"

7. If user chooses (2):
   - Mark those ingredients as unlinked in the recipe
   - Add a note in the recipe: `*(component not yet created)*`

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

#### Step 5: Calculate Total Nutrition & Divide by Servings
**Purpose**: Sum nutritional content across all ingredients and calculate per-serving values.

**Actions**:
1. Sum all nutritional values from Step 4 across all ingredients
2. Extract or prompt for servings count:
   - If servings was extracted in Step 3: Use that value
   - Otherwise: Prompt "How many servings does this recipe make?"
3. Divide all totals by servings count
4. Round all values to 2 significant figures

**Validation**:
- Servings count is a positive integer
- All nutritional values divided correctly

**Error Handling**:
- If servings is 0 or invalid: Prompt "Please enter valid servings (positive number):"

**Important Notes**:
- **IMPORTANT**: All nutritional values in final output are PER SERVING, not total.

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
   - **Nutrition Information** (per serving, table format)
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
- No missing nutritional data

**Error Handling**:
- If filename collision: Append timestamp or number (e.g., "chocolate-chip-cookies-2.md")
- If write fails: Report error and ask user to check directory permissions

**Important Notes**:
- **IMPORTANT**: Output must follow `templates/recipe.md` structure exactly. Read the template before generating output.
- **IMPORTANT**: All significant ingredients must link to components using relative paths: `../components/[category]/[name].md`
- All nutritional values should be clearly labeled with units (g, mg, IU, µg).
- Include flagged unit conversions as comments so user can verify.
- Scaling notes help future meal planning: if you use this recipe again, you'll see the scaling approach and supermarket constraints that informed the original amounts.

---

#### Step 8: Confirmation & Output
**Purpose**: Confirm success and show user where recipe is stored.

**Actions**:
1. Display success message with file location
2. Show brief summary of what was created
3. List any new components created
4. Provide next steps

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
└── components/            # Searched for ingredient linking
    ├── proteins/
    ├── vegetables/
    ├── starches/
    ├── fruits/
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
- [ ] All ingredients found in USDA database (or user clarified unclear ingredients)
- [ ] All significant ingredients linked to components (or marked as pantry staples)
- [ ] Component links use correct relative paths: `../components/[category]/[name].md`
- [ ] Nutritional values calculated and divided by servings correctly
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
- USDA API calls should be made for each ingredient; cache results within a session to avoid redundant lookups
- Interactive prompts should be clear and help users provide clarifications when needed
- All user input should be validated before proceeding to next steps

## Troubleshooting & Common Issues

### API Key Problems

**Issue**: "USDA_API_KEY environment variable not set"
- **Solution**: Ensure your `.env` file contains `USDA_API_KEY=your_key` or run `export USDA_API_KEY="your_key"` in terminal before running the command
- **If you don't have an API key**: Get one free at https://fdc.nal.usda.gov/api-key-signup.html (instant signup)

**Issue**: "API key appears invalid or expired"
- **Solution**: Generate a new API key from https://fdc.nal.usda.gov/api-key-signup.html and update your `.env` file

### API Connectivity Issues

**Issue**: USDA API returns errors (HTTP 5xx, timeouts, connection refused)
- **Expected Behavior**: Command automatically falls back to reference nutrition data
- **What You'll See**: "Note: Using reference nutrition data for '{ingredient}' (live API lookup unavailable)"
- **Action Required**: None—recipe will still be created with standard nutrition values
- **Best Practice**: Try again later if all ingredients show fallback warnings; live API may be temporarily unavailable

**Issue**: API returns HTML instead of JSON (403/404 errors)
- **Common Cause**: USDA API endpoint may have changed or your internet connection is blocked
- **Solution**: Check internet connectivity; if persistent, contact USDA FoodData Central support

### Accuracy & Data Quality

**Issue**: "Nutritional values seem off for ingredient X"
- **Cause**: Reference data uses standard USDA values; prepared/processed foods may vary
- **Solution**: After recipe creation, you can manually edit the nutrition table in the markdown file if needed
- **Tip**: For processed foods with nutrition labels, use the label values instead of generic USDA data

**Issue**: "I want to use specific nutrition data (from package label, etc.)"
- **Solution**: After the recipe is created, open the markdown file and edit the nutrition table directly
- **Format**: Keep the same table structure; values should be per serving

### Reference Data (Fallback Values)

The command includes pre-calculated reference nutrition data for common ingredients (based on USDA FoodData Central):
- Grains: quinoa, rice, wheat flour, oats
- Proteins: chicken, salmon, beef, tofu, eggs
- Vegetables: broccoli, spinach, carrots, beets, arugula
- Fruits: apple, banana, berries, orange
- Fats & Oils: olive oil, avocado oil, butter
- Nuts & Seeds: almonds, walnuts, flax seed
- Dairy: milk, yogurt, cheese

If an ingredient is not in this list and the API is unavailable, the command will prompt for clarification or ask if you'd like to use a similar ingredient's data.
