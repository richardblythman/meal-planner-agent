# Plan Weekly Meals

## Overview
Automate the weekly meal planning workflow by collecting your meal selections, validating them against available recipes, and generating a comprehensive meal plan document with shopping list and nutritional summary.

## Purpose & Value

### Workflow Being Automated
Planning meals for the week currently requires manually selecting dinners, mapping them to lunches (accounting for leftovers), creating a shopping list from ingredient lists, and calculating nutritional totals. This command automates all of those steps: collect meal selections, cross-reference the recipe database, generate the meal plan with leftover mappings, consolidate ingredients into a categorized shopping list, and calculate cumulative nutrition.

### Time/Effort Savings
- Eliminate manual shopping list creation (automatic ingredient consolidation)
- Automatic quantity aggregation across all meals
- Quick nutritional summary without manual calculation
- Clear leftovers mapping (dinner → lunch)
- Organized by grocery category for efficient shopping
- Recipe links embedded in the meal plan for easy reference

### Target Users
- Anyone planning weekly meals from a curated recipe database
- Users who want to see nutritional totals for the planned week
- Cooks managing meal prep and leftovers for lunch

## Command Invocation

### Command Name
```
/plan_weekly_meals
```

### Parameters
None - the command is fully interactive.

### Usage Examples
```bash
# Invoke the command
/plan_weekly_meals

# Then provide meal selections and answer prompts as requested
```

## Procedural Requirements

### Prerequisites
- `memory/recipes/` directory exists with individual recipe markdown files
- Each recipe file contains:
  - Recipe title (# header)
  - **Meal Tags**: breakfast | lunch | dinner | snack
  - **Servings** count
  - **Ingredients** list with quantities
  - **Nutrition per Serving** (at minimum: calories, protein, carbs, fat)
  - **Notes** section mentioning leftovers behavior (how many days it keeps, reheating notes)
- `memory/meals/` directory exists (will be created if missing)
- Recipes follow the standardized format documented in CLAUDE.md

### Step-by-Step Workflow

#### Step 1: Initialize & Verify Prerequisites
**Purpose**: Ensure recipe directory exists and is readable.

**Actions**:
1. Check if `memory/recipes/` directory exists
2. List all `.md` files in `memory/recipes/` directory (these are available recipes)
3. Filter for recipes tagged with "dinner" (check the **Meal Tags** line)
4. Display greeting and instructions

**Validation**:
- Recipe directory exists and contains at least one dinner recipe
- All dinner recipes are readable and properly formatted

**Error Handling**:
- If directory doesn't exist: Create `memory/recipes/` and inform user to add recipes first
- If no dinner recipes found: Prompt user to add recipes before proceeding (using `/add_recipe` command)
- If recipe file is malformed (missing sections): Flag the problematic file and skip it

**Important Notes**:
- **IMPORTANT**: Only list recipes that have "dinner" in their **Meal Tags** line. Skip recipes with only breakfast/lunch/snack tags.

---

#### Step 2: Collect Starting Date
**Purpose**: Get the Sunday date for this week's meal plan (used for file naming).

**Actions**:
1. Prompt user: "What is the Sunday date for this week? (format: YYMMDD, e.g., 250112 for Jan 12, 2025):"
2. Accept and validate the date format
3. Store the date for file naming

**Validation**:
- Date is in YYMMDD format (6 digits)
- Date is valid (not a future date beyond reasonable planning window, e.g., not 2 months out)

**Error Handling**:
- If format invalid: Prompt "Please enter date in YYMMDD format (e.g., 250112):"
- If date parsing fails: Ask user to re-enter
- If date is unreasonable (e.g., too far in future): Warn and ask to confirm

**Important Notes**:
- **IMPORTANT**: This date will be used for the output filename: `memory/meals/[YYMMDD]-meal-plan.md`

---

#### Step 3: Ask How Many Days Cooking
**Purpose**: Determine how many days of the week (Monday-Friday) will have cooked meals.

**Actions**:
1. Prompt user: "How many days will you be cooking this week? (1-5):"
2. Accept and validate input (must be integer 1-5)
3. Store the number for meal selection

**Validation**:
- Input is numeric
- Value is between 1 and 5 (inclusive)

**Error Handling**:
- If not numeric: Prompt "Please enter a number between 1 and 5:"
- If outside range: Prompt "Please enter a number between 1 and 5 (you're planning 5 days: Mon-Fri):"

**Important Notes**:
- **IMPORTANT**: User does not specify which days—just the count. The plan will include Day 1 (Monday) through Day N (where N = number of cooking days).

---

#### Step 4: Collect Dinner Meal Selections
**Purpose**: Get user's dinner meal selections for each cooking day.

**Actions**:
1. For each cooking day (1 through N from Step 3):
   - Prompt: "Day [number] dinner (Monday/Tuesday/etc.): "
   - Accept meal name (user types exact recipe name from `memory/recipes/`)
   - Validate that the recipe exists and has "dinner" tag
   - If valid: Store the selection
   - If invalid: Prompt "Recipe '[name]' not found or not tagged as dinner. Available dinner recipes: [list]. Try again:"

2. Repeat until all N days have dinner selections

**Validation**:
- Each meal name matches a file in `memory/recipes/`
- Each recipe has "dinner" in its **Meal Tags**
- No empty entries (user must provide a recipe for each day)

**Error Handling**:
- If recipe not found: Show available dinner recipes and ask again
- If user enters a non-dinner recipe: Explain it's not tagged for dinner and ask again
- If duplicate meals on same day: Warn and ask to confirm (allow duplicates if intentional)

**Important Notes**:
- **IMPORTANT**: Accept recipe names as provided by user. Match against filenames in `memory/recipes/` (names without `.md` extension).
- Be flexible with capitalization and spacing—try case-insensitive matching and trim whitespace.
- Show a helpful list: "Available dinner recipes: Beef and Broccoli Bowls, Salmon Bowls, Soba Noodle Bowls, ..."

---

#### Step 5: Load Recipe Data
**Purpose**: Read nutritional and ingredient data from selected recipe files.

**Actions**:
1. For each selected dinner recipe:
   a. Open the recipe file: `memory/recipes/[recipe-name].md`
   b. Extract:
      - Recipe title
      - Ingredients list (with quantities)
      - Nutrition per serving (calories, protein, carbs, fat, and any other nutrients listed)
      - Servings count
      - **Meal Tags** (verify "dinner" is present)
      - Notes section (check for leftovers info, e.g., "Leftovers keep 2-3 days")
   c. Store extracted data in memory for later use

2. Validate all data extraction:
   - All recipes loaded successfully
   - Nutrition data is complete (at least calories, protein, carbs, fat)

**Validation**:
- All recipe files read without error
- Required sections present in each recipe

**Error Handling**:
- If recipe file not found: Report error and ask user to verify recipe name
- If nutrition data missing: Flag missing nutrients and ask if user wants to continue or re-select
- If file is corrupted/malformed: Skip that recipe and ask user to fix the file or re-select

**Important Notes**:
- **IMPORTANT**: Parse nutrition values carefully. Look for tables, bullet points, or structured formats. Round values as found (don't recalculate).

---

#### Step 6: Create Meal Plan with Leftovers Mapping
**Purpose**: Build the final meal plan showing dinners and their corresponding lunch leftovers.

**Actions**:
1. Create meal plan structure:
   - For each cooking day N:
     - Day N (e.g., "Day 1 - Monday"): [Dinner Recipe Name] (link to file)
     - Day N Lunch: [Same Recipe Name] (leftovers from Day N Dinner)

2. Example output structure (for 3 cooking days):
   ```
   Day 1 - Monday
   Dinner: Beef and Broccoli Bowls

   Day 1 Lunch (Tuesday)
   Leftovers: Beef and Broccoli Bowls

   Day 2 - Tuesday
   Dinner: Salmon Bowls

   Day 2 Lunch (Wednesday)
   Leftovers: Salmon Bowls

   Day 3 - Wednesday
   Dinner: Soba Noodle Bowls

   Day 3 Lunch (Thursday)
   Leftovers: Soba Noodle Bowls
   ```

3. Store the meal plan structure for later output

**Validation**:
- All days have dinner selections
- All lunches correctly mapped to preceding dinner

**Error Handling**:
- (No typical errors at this stage—structure is generated from prior selections)

**Important Notes**:
- **IMPORTANT**: The lunch for Day N is always from the dinner of Day N (same recipe, leftovers).
- Day 5 (Friday) dinner leftovers would be lunch on Saturday (which is outside the planning window, but still include it).

---

#### Step 7: Consolidate Ingredients & Build Shopping List
**Purpose**: Aggregate all ingredients from dinners into a deduplicated, category-organized shopping list with cumulative quantities.

**Actions**:
1. Initialize ingredient categories:
   - Proteins
   - Vegetables
   - Grains & Starches
   - Fruits
   - Dairy & Eggs
   - Pantry & Spices
   - Oils & Fats
   - Other

2. For each selected dinner recipe:
   a. Parse the **Ingredients** list:
      - Extract ingredient name, quantity, and unit (e.g., "2 cups flour" → name: "flour", qty: 2, unit: "cups")
   b. Infer category for each ingredient (based on common categories above)
   c. Accumulate quantities:
      - If ingredient already exists in the list: Add quantities (converting units if necessary)
      - If new ingredient: Add to appropriate category
   d. Include notes on unit conversions if quantities were added from different units

3. Organize shopping list by category (in order: Proteins, Vegetables, Grains, Fruits, Dairy, Pantry, Oils, Other)

4. Store shopping list structure for later output

**Validation**:
- All ingredients parsed correctly
- Quantities aggregated without loss of data
- Categories are logical and consistent

**Error Handling**:
- If ingredient unit is unclear (e.g., "to taste", "pinch"): Flag and note in shopping list (e.g., "Salt to taste")
- If ingredient name is ambiguous: Use as-is but note for user clarification
- If category assignment is uncertain: Place in "Other" category

**Important Notes**:
- **IMPORTANT**: Use common unit conversions (e.g., 1 cup ≈ 240ml, 1 tablespoon ≈ 15ml) to consolidate similar ingredients. Flag any conversions.
- Consolidate similar ingredients intelligently: "salt" + "kosher salt" = "salt (2 tsp total)", etc.
- Include original quantities in parentheses if helpful (e.g., "Beef: 900g (2 × 450g packs)")

---

#### Step 8: Calculate Nutritional Summary
**Purpose**: Aggregate nutrition from all dinners (note: lunches are leftovers, so same nutrition counted again if user eats both dinner and lunch that day).

**Actions**:
1. For each selected dinner recipe:
   a. Extract nutrition per serving: calories, protein, carbs, fat, and any other nutrients listed
   b. Multiply by servings count (if provided; typically recipes are scaled to 1 serving or N servings)
   c. Accumulate totals across all dinners

2. Calculate summary statistics:
   - Total calories across all dinners
   - Total protein, carbs, fat
   - Any other nutrients listed in recipes (vitamins, minerals, etc.)
   - Average per day (divide totals by number of cooking days)

3. Store nutritional summary for later output

**Validation**:
- All nutrition values extracted correctly
- Totals calculated without error
- Units consistent (grams for macros, mg/µg for micros)

**Error Handling**:
- If nutrition data missing for a recipe: Note in summary that data is incomplete
- If units vary (e.g., some recipes use mg, some use g): Normalize to consistent units

**Important Notes**:
- **IMPORTANT**: Calculate totals for dinners only (not lunches), since lunches are the same meals (leftovers).
- This is a cumulative weekly total, not a daily intake target—useful for assessing variety and balance across the week.

---

#### Step 9: Create Output Markdown File
**Purpose**: Write the complete meal plan with meals, shopping list, nutritional summary, and recipe links to a markdown file.

**Actions**:
1. Generate filename: `memory/meals/[YYMMDD]-meal-plan.md` (where [YYMMDD] is from Step 2)

2. Create markdown file with these sections in order:
   a. **Title**: "Weekly Meal Plan - [Sunday Date]"
   b. **Overview**: Brief summary (e.g., "5 days of cooking (Monday-Friday), 5 dinners with lunch leftovers")
   c. **Meal Plan**: Full listing of all dinners and corresponding lunch leftovers with recipe links
   d. **Shopping List**: Organized by category with aggregated quantities
   e. **Nutritional Summary**: Weekly totals and daily averages
   f. **Recipe Links**: Full paths to recipe files for easy reference

3. Format details:

   **Title & Overview**:
   ```
   # Weekly Meal Plan
   **Week of:** Sunday, [Date formatted as Jan 12, 2025]
   **Cooking Days:** [Number] (Monday through [Day N])
   **Total Dinners:** [Number]
   **Total Lunches (Leftovers):** [Number]
   ```

   **Meal Plan Section**:
   ```
   ## Meal Plan

   ### Day 1 - Monday
   **Dinner:** [Recipe Name] - `memory/recipes/[recipe-filename].md`

   ### Day 1 Lunch (Tuesday)
   **Leftovers:** [Recipe Name] from Day 1 Dinner

   ### Day 2 - Tuesday
   **Dinner:** [Recipe Name] - `memory/recipes/[recipe-filename].md`

   ### Day 2 Lunch (Wednesday)
   **Leftovers:** [Recipe Name] from Day 2 Dinner

   [... repeat for all cooking days ...]
   ```

   **Shopping List Section**:
   ```
   ## Shopping List by Category

   ### Proteins
   - [Ingredient]: [Quantity] [Unit]
   - [Ingredient]: [Quantity] [Unit]

   ### Vegetables
   - [Ingredient]: [Quantity] [Unit]

   [... repeat for all categories ...]
   ```

   **Nutritional Summary Section**:
   ```
   ## Weekly Nutritional Summary

   ### Total (All Dinners)
   - Calories: [Total]
   - Protein: [Total]g
   - Carbs: [Total]g
   - Fat: [Total]g
   - [Other nutrients as available]

   ### Daily Average
   - Calories: [Average]
   - Protein: [Average]g
   - Carbs: [Average]g
   - Fat: [Average]g
   - [Other nutrients as available]
   ```

   **Recipe Links Section**:
   ```
   ## Recipe Files
   - [Recipe Name]: `memory/recipes/[recipe-filename].md`
   - [Recipe Name]: `memory/recipes/[recipe-filename].md`

   [... one entry per unique recipe selected ...]
   ```

4. Write file to disk at `memory/meals/[YYMMDD]-meal-plan.md`

**Validation**:
- File created successfully at correct path
- All sections present with complete data
- Markdown properly formatted (headers, lists, links)
- File is readable and properly saved

**Error Handling**:
- If filename collision exists: Warn user that file will be overwritten, ask to confirm
- If write fails: Report error (permissions, disk space, etc.) and ask user to check directory

**Important Notes**:
- **IMPORTANT**: File should be immediately usable—all links should be valid relative paths.
- Include comments or notes if any data was incomplete (e.g., "Note: Nutritional data incomplete for [Recipe]").
- Format dates in human-readable format (e.g., "Monday, January 12, 2025") for clarity.

---

#### Step 10: Confirmation & Output
**Purpose**: Confirm success and provide user with file location and quick summary.

**Actions**:
1. Display success message
2. Show file location
3. Provide summary of what was created
4. Suggest next steps

**Output Format**:
```
✓ Weekly Meal Plan Created Successfully!

File: memory/meals/250112-meal-plan.md

Summary:
- Week of: Sunday, January 12, 2025
- Cooking Days: 5 (Monday through Friday)
- Total Dinners: 5
- Total Lunches (Leftovers): 5
- Recipes Used: Beef and Broccoli Bowls, Salmon Bowls, Soba Noodle Bowls, Freekeh Bowls, Crispy Potato Bowls

Shopping List:
- Categories: 8 (Proteins, Vegetables, Grains, Fruits, Dairy, Pantry, Oils, Other)
- Total Ingredients: 47

Nutritional Summary (All Dinners):
- Total Calories: 12,450
- Total Protein: 400g
- Total Carbs: 1,050g
- Total Fat: 300g
- Daily Average: 2,490 cal, 80g protein, 210g carbs, 60g fat

The meal plan is ready to use for shopping and cooking!
You can view the full plan and shopping list in the generated file.
```

---

## File Structure & Paths

### Files Created
```
memory/
└── meals/
    ├── 250112-meal-plan.md
    ├── 250119-meal-plan.md
    └── [YYMMDD]-meal-plan.md
```

### Path Conventions
- **Absolute paths**: All recipe file references use relative paths from project root (e.g., `memory/recipes/[recipe-name].md`)
- **File naming**: `[YYMMDD]-meal-plan.md` where YYMMDD is the Sunday date of the week (year first, no hyphens)
- **Recipe links**: Use markdown file links in format: `[Recipe Name](memory/recipes/[filename].md)`

## Validation & Testing

### Success Criteria
- [ ] Recipes loaded from `memory/recipes/` without error
- [ ] All dinner selections are valid and tagged with "dinner"
- [ ] Meal plan includes all dinners and corresponding lunch leftovers
- [ ] Shopping list consolidates and aggregates ingredients by category
- [ ] Nutritional totals calculated correctly from recipe data
- [ ] Output file created at `memory/meals/[YYMMDD]-meal-plan.md`
- [ ] File contains all sections: meal plan, shopping list, nutrition summary, recipe links
- [ ] All recipe links are valid paths
- [ ] User receives clear confirmation with file location

## Integration Notes

### Related Commands
- `/add_recipe`: Add new recipes to `memory/recipes/` to expand available meal options
- `/generate_shopping_list`: (Future) More advanced shopping list features (sorting, unit consolidation, cost tracking)
- `/analyze_symptoms`: (Future) Correlate meal plans with symptom logs

### Related Workflows
- **Meal Planning**: This command creates the weekly plan; output drives shopping and cooking
- **Shopping**: Use the generated shopping list to order groceries
- **Symptom Tracking**: Link meal plan with Bristol scale logs in `memory/symptom_log.md` for correlation analysis

### Notes for Implementation
- This command should be flexible with user input (case-insensitive recipe matching, whitespace trimming)
- Ingredient consolidation should handle common synonyms (e.g., "flour" + "all-purpose flour")
- Unit conversions should be logged for user transparency
- Output markdown should be human-readable and immediately usable for shopping/cooking reference
