# Add Recipe

## Overview
Automate the capture and standardization of recipes from various sources. Paste raw recipe text, and this command will parse it, automatically estimate nutritional content using USDA FoodData Central, and create a standardized markdown file ready for meal planning and shopping lists.

## Purpose & Value

### Workflow Being Automated
Collecting recipes from books, websites, and other sources currently requires manual data entry and formatting. This command extracts recipe information from pasted text, standardizes the format, and automatically calculates nutritional information per serving using USDA data—eliminating manual nutrition lookups and formatting inconsistencies.

### Time/Effort Savings
- Eliminate manual nutrition research and calculation
- Consistent formatting across all recipes
- Automatic ingredient parsing and unit conversion
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
- `kb/recipes/` directory exists in your knowledge base
- Node.js or access to JavaScript execution environment (for API calls and JSON parsing)

### Step-by-Step Workflow

#### Step 1: Initialize & Verify Prerequisites
**Purpose**: Ensure API key is configured and output directory exists.

**Actions**:
1. Check if `USDA_API_KEY` environment variable is set
2. Verify `kb/recipes/` directory exists; create if needed
3. Display greeting and instructions

**Validation**:
- API key is accessible
- Directory is writable

**Error Handling**:
- If API key not found: Prompt user with instructions to set `USDA_API_KEY` environment variable and re-run
- If directory doesn't exist: Create it automatically

**Important Notes**:
- **IMPORTANT**: The USDA API key must be set before proceeding. Store it as an environment variable for security.

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

**Important Notes**:
- **IMPORTANT**: Be thorough with ingredient matching. If the USDA database returns multiple results, show options to user.
- **IMPORTANT**: Flag all unit conversions so user can verify accuracy.
- Handle ingredient variations: "flour" might be "all-purpose flour", "butter" might be "salted butter", etc.

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

#### Step 6: Collect Missing Metadata
**Purpose**: Gather any metadata not extracted from the recipe text.

**Actions**:
1. For each metadata field, check if it was extracted in Step 3:
   - Servings (already confirmed in Step 5)
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
**Purpose**: Generate the final recipe markdown file with consistent formatting.

**Actions**:
1. Generate filename: Convert recipe title to lowercase, replace spaces with hyphens, remove special characters
   - Example: "Chocolate Chip Cookies" → "chocolate-chip-cookies.md"
2. Create markdown file at `kb/recipes/[filename]`
3. Structure the markdown with these sections in order:
   - **Title** (# header)
   - **Story/Intro** (optional, if present)
   - **Ingredients** (bulleted list with standardized formatting)
   - **Instructions** (numbered list)
   - **Metadata** (Servings, Prep Time, Cook Time, Difficulty, Cuisine Type, Dietary Restrictions, Tags, Source)
   - **Nutrition Information** (per serving, table or structured format)

4. Format details:
   - Title: `# Recipe Title`
   - Story: Plain text paragraph(s)
   - Ingredients: `- [amount] [unit] [ingredient name]`
     - Include flagged conversions as comments: `- 256g flour (converted from 2 cups)`
   - Instructions: Numbered steps, one per line
   - Metadata: Key-value format or structured
   - Nutrition: Table format with values rounded to 2 sig figs
     ```
     | Nutrient | Per Serving |
     |----------|-------------|
     | Calories | 240 |
     | Protein | 8.2g |
     | Carbs | 32g |
     | Fat (Total) | 8.5g |
     | Saturated Fat | 3.2g |
     | Unsaturated Fat | 4.8g |
     | Fiber | 2.1g |
     | Vitamin A | 450 IU |
     | Vitamin B12 | 0.5 µg |
     | Vitamin C | 5.2mg |
     | Vitamin D | 0 IU |
     | Vitamin E | 1.2mg |
     | Vitamin K | 8.5 µg |
     | Calcium | 120mg |
     | Iron | 1.8mg |
     | Sodium | 280mg |
     | Potassium | 350mg |
     | Magnesium | 45mg |
     ```

5. Write file to disk

**Validation**:
- File created successfully at `kb/recipes/[filename]`
- File contains all required sections
- Markdown is properly formatted
- No missing nutritional data

**Error Handling**:
- If filename collision: Append timestamp or number (e.g., "chocolate-chip-cookies-2.md")
- If write fails: Report error and ask user to check directory permissions

**Important Notes**:
- **IMPORTANT**: File should be ready to use immediately—no additional formatting needed by user.
- All nutritional values should be clearly labeled with units (g, mg, IU, µg).
- Include flagged unit conversions as comments so user can verify.

---

#### Step 8: Confirmation & Output
**Purpose**: Confirm success and show user where recipe is stored.

**Actions**:
1. Display success message with file location
2. Show brief summary of what was created
3. Provide next steps

**Output Format**:
```
✓ Recipe added successfully!

File: kb/recipes/chocolate-chip-cookies.md

Recipe Summary:
- Title: Chocolate Chip Cookies
- Servings: 24 cookies
- Prep Time: 15 minutes
- Cook Time: 12 minutes
- Difficulty: 2/5
- Cuisine: American
- Tags: dessert, quick, weeknight

The recipe is ready to use for meal planning and shopping lists!
```

---

## File Structure & Paths

### Files Created
```
kb/
└── recipes/
    ├── chocolate-chip-cookies.md
    ├── pasta-carbonara.md
    └── [recipe-name].md
```

### Path Conventions
- **Absolute paths**: All operations use absolute paths to `kb/recipes/`
- **Relative paths**: Not used; all file operations reference full path from project root
- **Recipe filenames**: Lowercase, hyphens instead of spaces, no special characters

## Validation & Testing

### Success Criteria
- [ ] Recipe text is parsed correctly (title, ingredients, instructions, metadata extracted)
- [ ] All ingredients found in USDA database (or user clarified unclear ingredients)
- [ ] Nutritional values calculated and divided by servings correctly
- [ ] Markdown file created at `kb/recipes/[recipe-name].md`
- [ ] File contains all standardized sections with proper formatting
- [ ] All nutritional values shown per serving with 2 significant figures
- [ ] Unit conversions flagged for user verification
- [ ] User receives clear confirmation with file location

## Integration Notes

### Related Workflows
- **Meal Planning**: Use recipes from `kb/recipes/` to create weekly meal plans
- **Shopping Lists**: Extract ingredients from selected recipes to generate shopping lists
- **Recipe Search**: Query recipes by tags, cuisine type, dietary restrictions, or difficulty

### Notes for Implementation
- This command should handle raw text input flexibly, accommodating variations in recipe formatting from different sources
- USDA API calls should be made for each ingredient; cache results within a session to avoid redundant lookups
- Interactive prompts should be clear and help users provide clarifications when needed
- All user input should be validated before proceeding to next steps
