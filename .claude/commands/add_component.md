# Add Component

## Overview
Add a new food component (protein, vegetable, starch, or sauce) to the knowledge base by providing a link to a grocery website product page. The command extracts product information and creates a standardized component file ready for mix-and-match meal planning.

## Purpose & Value

### Workflow Being Automated
When you find a product on your grocery website, manually copying the nutritional information and formatting it consistently is tedious. This command takes a product URL, extracts the relevant data, and creates a properly formatted component file.

### Time/Effort Savings
- No manual copying of nutrition labels
- Consistent formatting across all components
- Direct link to product preserved for easy reordering
- Ready-to-use components for meal planning immediately

### Target Users
- Anyone building a component library for ad-hoc meal planning
- Users who prefer "protein + veg + starch" style cooking over fixed recipes

## Command Invocation

### Command Name
```
/add_component
```

### Parameters
None - the command is interactive.

### Usage Examples
```bash
# Invoke the command
/add_component

# Then provide the grocery website URL when prompted
```

## Procedural Requirements

### Prerequisites
- `memory/components/` directory structure exists (proteins/, vegetables/, starches/, sauces/)
- `templates/component.md` template file exists
- Internet access to fetch product page

### Step-by-Step Workflow

#### Step 1: Collect Product URL
**Purpose**: Get the grocery website product link from the user.

**Actions**:
1. Prompt user: "Paste the grocery website URL for the product:"
2. Store URL for fetching

**Validation**:
- URL is valid and accessible
- URL points to a product page (not a category or search page)

**Error Handling**:
- If URL invalid: "Please provide a valid product URL"
- If page not accessible: "Could not access that URL. Please check it's correct and try again"

---

#### Step 2: Fetch Product Information
**Purpose**: Extract product details from the webpage.

**Actions**:
1. Fetch the product page
2. Extract:
   - Product name
   - Unit size (e.g., 2kg, 500g, 4-pack)
   - Ingredients list
   - Nutrition information (per 100g or per serving as available)

**Validation**:
- Product name found
- At least some nutritional data found

**Error Handling**:
- If product name not found: Prompt "What is the product name?"
- If nutrition data not found: Prompt user to enter manually

---

#### Step 3: Confirm and Collect Missing Data
**Purpose**: Verify extracted data and fill gaps.

**Actions**:
1. Display extracted information to user for confirmation
2. Ask user to confirm or correct:
   - Product name
   - Category (Protein / Vegetable / Starch / Sauce)
   - Unit size
   - Serving size (how much they typically use per meal)
3. If nutrition data incomplete, prompt for missing values:
   - Energy (kJ/kcal)
   - Fat, Saturates
   - Carbs, Sugars
   - Fiber
   - Protein
   - Salt

**Validation**:
- Category is one of: Protein, Vegetable, Starch, Sauce
- Unit size and serving size are provided
- Core nutrition values are present

**Error Handling**:
- If category invalid: Re-prompt with options
- If serving size missing: "What serving size do you typically use (in grams)?"

---

#### Step 4: Create Component File
**Purpose**: Generate the standardized component markdown file.

**Actions**:
1. Generate filename: Convert product name to lowercase, replace spaces with hyphens, remove special characters
   - Example: "Chicken Breast Fillets" → "chicken-breast-fillets.md"
2. Determine target directory based on category:
   - Protein → `memory/components/proteins/`
   - Vegetable → `memory/components/vegetables/`
   - Starch → `memory/components/starches/`
   - Sauce → `memory/components/sauces/`
3. Create markdown file using template structure:

```markdown
# [Product Name]

**Category**: [Category]
**Unit Size**: [Xg / Xkg] (as purchased)
**Serving Size**: [Xg] (cooked)
**Link**: [Product Name](URL)

## Ingredients
[Ingredient list from product page]

## Nutrition per Serving
- Energy: [X]kJ / [X]kcal
- Fat: [X]g
- Saturates: [X]g
- Carbs: [X]g
- Sugars: [X]g
- Fiber: [X]g
- Protein: [X]g
- Salt: [X]g
```

4. Calculate nutrition per serving if product page shows per 100g:
   - Scale values based on serving size
   - Round to 1 decimal place

5. Write file to disk

**Validation**:
- File created successfully
- All required fields populated
- Nutrition values scaled correctly to serving size

**Error Handling**:
- If filename collision: Append number (e.g., "chicken-breast-2.md")
- If write fails: Report error and check directory permissions

---

#### Step 5: Confirmation & Output
**Purpose**: Confirm success and show user the result.

**Actions**:
1. Display success message with file location
2. Show brief summary

**Output Format**:
```
✓ Component added successfully!

File: memory/components/proteins/chicken-breast-fillets.md

Component Summary:
- Name: Chicken Breast Fillets
- Category: Protein
- Unit Size: 1kg
- Serving Size: 150g
- Link: [preserved for reordering]

Nutrition per Serving (150g):
- Energy: 231kcal
- Protein: 43g
- Fat: 5g
- Carbs: 0g

Ready to use in meal planning! Example:
  "Tuesday dinner: chicken-breast-fillets + broccoli + rice"
```

---

## File Structure & Paths

### Files Created
```
memory/
└── components/
    ├── proteins/
    │   └── chicken-breast-fillets.md
    ├── vegetables/
    │   └── broccoli.md
    ├── starches/
    │   └── white-rice.md
    └── sauces/
        └── teriyaki-sauce.md
```

### Path Conventions
- **Category determines directory**: Protein → proteins/, Vegetable → vegetables/, etc.
- **Filenames**: Lowercase, hyphens instead of spaces, no special characters

## Validation & Testing

### Success Criteria
- [ ] Product URL fetched successfully
- [ ] Product name, unit size, ingredients, and nutrition extracted (or collected from user)
- [ ] Category assigned correctly
- [ ] Serving size collected from user
- [ ] Nutrition values scaled to serving size
- [ ] Markdown file created in correct category directory
- [ ] File follows template structure exactly
- [ ] Link to product preserved for reordering
- [ ] User receives clear confirmation with file location

## Integration Notes

### Related Workflows
- **Meal Planning**: Use components in `memory/components/` for ad-hoc meals like "chicken + peas + rice"
- **Shopping Lists**: Extract components from meal plan to generate shopping lists with product links
- **Nutrition Tracking**: Agent sums component nutrition to calculate meal totals

### Notes for Implementation
- Grocery website structure varies; be flexible with HTML parsing
- If automated extraction fails, fall back to user input
- Always preserve the original product link for easy reordering
- Nutrition is often shown per 100g; scale to user's serving size
