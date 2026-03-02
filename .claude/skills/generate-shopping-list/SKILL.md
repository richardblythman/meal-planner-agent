---
name: generate-shopping-list
description: >
  **Shopping List Generator**: Consolidates ingredients from a weekly meal plan into an organized, deduplicated shopping list grouped by grocery category. Use this skill whenever the user wants to create a shopping list from their meal plan, needs to know what to buy for the week, wants to update an existing shopping list after meal plan changes, or asks about ingredient quantities for planned meals. Also trigger when the user says "what do I need to buy", "shopping list", "grocery list", "ingredients for this week", or references buying food for planned meals.
---

# Generate Shopping List

You produce a consolidated, organized shopping list from a weekly meal plan. The user shops online weekly and doesn't track pantry inventory — so the list should include everything needed for the week's meals, with a separate "check stock" section for common pantry staples.

## Where to Find Things

- **Current meal plan**: `memory/weeks/[MMDDYY]/meals.md` — The week's meal selections with recipe links
- **Recipes**: `memory/recipes/*.md` — Full ingredient lists with quantities
- **Components**: `memory/components/{proteins,vegetables,starches,fruits,dairy}/*.md` — Individual items with unit sizes and purchase links
- **Previous shopping lists**: `memory/weeks/[MMDDYY]/shopping-list.md` — Past lists for format reference

## Workflow

### 1. Identify the Meal Plan

If the user doesn't specify which week, check for the most recent `meals.md` in `memory/weeks/`. If no meal plan exists yet, tell the user to plan meals first (or offer to help with that).

### 2. Extract All Ingredients

For each meal in the plan:
- Read the linked recipe file and extract the full ingredients list with quantities
- For component meals (no recipe file), read each component and note standard serving sizes
- For eaten-out meals or external events, skip — no ingredients needed

### 3. Consolidate and Deduplicate

Many recipes share ingredients. Combine them:
- Sum quantities for the same ingredient across recipes (e.g., 3 red onions total if used in 3 recipes)
- Note which days/recipes each ingredient is used for
- Round up to practical purchase quantities (you can't buy 0.7 of an onion)
- Reference component files for typical package sizes (e.g., "Tesco 4 Salmon Fillets 440g")

### 4. Categorize

Organize into these categories (matching the user's established format):

1. **Proteins** — Meat, fish, seafood, eggs, beans/legumes
2. **Vegetables** — Fresh vegetables
3. **Fruits** — Fresh fruits (avocados, limes, etc.)
4. **Grains & Starches** — Rice, pasta, noodles, potatoes, quinoa
5. **Dairy & Eggs** — Milk products, kefir, sour cream (eggs cross-listed with proteins)
6. **Frozen** — Frozen vegetables, frozen fries, etc.
7. **Fresh Herbs & Aromatics** — Cilantro, ginger, garlic, spring onions, chillies
8. **Pantry Items (Check Stock)** — Oils, vinegars, spices, sauces, seasonings

### 5. Format the Output

Use tables for fresh items (Item | Quantity | Notes) and checklists for pantry items. Include:

- A header with the week dates and number of meals planned
- Category sections with tables
- A "Shopping Summary by Store Section" that reorganizes by how you'd walk through a grocery store (Fresh Produce, Meat & Fish Counter, Dairy & Chilled, Canned & Dry Goods, Frozen)
- A Notes section with practical tips: ingredient overlap, ripeness advice, storage tips, total unique ingredients count

### 6. Save the File

Write to `memory/weeks/[MMDDYY]/shopping-list.md`.

## Key Principles

- **Practical quantities**: Round to purchasable amounts. If a recipe needs 40g of ginger, note "~30g piece" since you can't buy exactly 40g.
- **Package awareness**: Component files sometimes include pack sizes and store links. Use these when available.
- **Cross-recipe efficiency**: Highlight when one purchase covers multiple recipes (e.g., "Red onions: 3 large — used in Days 1, 2, and 4").
- **Pantry vs. fresh**: Spices, oils, vinegars, and dry seasonings go in the "check stock" section since the user likely has them. Everything else is assumed to be purchased fresh weekly.
- **FODMAP awareness**: If the user is on an elimination protocol (check `memory/fodmap_plan.md`), flag any ingredients that may need substitution and note the suggested alternatives.
