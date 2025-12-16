# 🍽️ Meal Planner Dashboard

---

## 📅 Week Overview

| | |
|---|---|
| **Week Starting** | Sunday, December 14, 2025 |
| **Cooking Days** | 4 (Monday – Thursday) |
| **Total Meals** | 8 (4 dinners + 4 lunch leftovers) |

---

## 🗓️ Weekly Meal Plan

> Click any meal name to view the full recipe

| Day | Dinner | Lunch (Next Day) |
|---|---|---|
| **Monday** | [Beef and Broccoli Bowls](memory/recipes/beef-and-broccoli-bowls.md) | Leftovers |
| **Tuesday** | [Crispy Potato & Smoked Salmon Power Bowls](memory/recipes/crispy-potato-and-smoked-salmon-power-bowls.md) | Leftovers |
| **Wednesday** | [Freekeh Bowls with Caramelized Onions, Warm Tomatoes & Fish](memory/recipes/freekeh-bowls-with-caramelized-onions-warm-tomatoes-and-seared-fish.md) | Leftovers |
| **Thursday** | [Ginger Peanut Soba Noodle Bowls](memory/recipes/ginger-peanut-soba-noodle-bowls.md) | Leftovers |

---

## 🛒 Shopping & Logistics

### Shopping List
> 📋 [View consolidated shopping list →](memory/weeks/251214/shopping-list.md)

**What to buy:**
- Proteins (beef, salmon, sea bass)
- Fresh vegetables (bok choy, avocados, mushrooms, etc.)
- Grains & pantry staples
- Specialty items (kimchi, miso, dukkah)

---

## 📊 Analysis & Details

### Meal Planning
- 📋 [Full weekly meal plan](memory/weeks/251214/meals.md) – detailed day-by-day breakdown
- 🔬 [Nutritional analysis](memory/weeks/251214/nutrition.md) – macros, micros, and dietary insights

---

## 🎯 Quick Reference

### Recipe Directory

All recipes in one place: [`memory/recipes/`](memory/recipes/)

**This Week's Recipes:**
- 🍗 [Beef and Broccoli Bowls](memory/recipes/beef-and-broccoli-bowls.md)
- 🐟 [Crispy Potato & Smoked Salmon Power Bowls](memory/recipes/crispy-potato-and-smoked-salmon-power-bowls.md)
- 🌾 [Freekeh Bowls with Caramelized Onions, Warm Tomatoes & Fish](memory/recipes/freekeh-bowls-with-caramelized-onions-warm-tomatoes-and-seared-fish.md)
- 🍜 [Ginger Peanut Soba Noodle Bowls](memory/recipes/ginger-peanut-soba-noodle-bowls.md)

---

## 📝 Coming Up

- [ ] Review nutritional breakdown
- [ ] Place online grocery order
- [ ] Prep ingredients or start meal prep
- [ ] Log digestive observations (if applicable)

---

## 🤖 About the Meal Planning Agent

The Meal Planning Agent is your personal nutrition assistant that helps you plan balanced meals, track nutritional intake, manage shopping lists, and investigate potential food-related digestive issues.

### Core Workflows

1. **Meal Planning & Optimization** – Select meals for the week, and the agent analyzes nutritional completeness, suggests improvements, and identifies gaps
2. **Shopping List Generation** – Consolidates ingredients from your selected meals into organized shopping lists
3. **Digestive Health Analysis** – Correlates Bristol scale symptom logs with meals consumed to help identify potential food triggers

### System Instructions

📖 **[Read the full system prompt](CLAUDE.md)** – Contains detailed information about how the agent works, design decisions, and conventions.

### Available Commands

The agent provides specialized commands for common workflows:

- **[/plan_weekly_meals](.claude/commands/plan_weekly_meals.md)** – Analyze your meal selections for nutritional balance and identify gaps
- **[/add_recipe](.claude/commands/add_recipe.md)** – Format and add new recipes to the knowledge base
- **[/create_agent_command](.claude/commands/create_agent_command.md)** – Create new custom agent commands
- **[/create_claude_md](.claude/commands/create_claude_md.md)** – System setup and configuration

### Knowledge Base

All your meal planning data is stored in the `memory/` directory:

- **`memory/recipes/`** – Your recipe library with full nutritional breakdowns
- **`memory/weeks/[MMDDYY]/`** – Weekly meal plans, nutritional analysis, and shopping lists organized by week
- **`memory/symptom_log.md`** – Bristol scale entries for digestive health tracking
- **`memory/nutritional_targets.md`** – Your personal nutritional goals and targets

### Getting Started

1. Explore the [full system prompt](CLAUDE.md) to understand the agent's philosophy and capabilities
2. Use `/plan_weekly_meals` to analyze meal plans for nutritional balance
3. Use `/add_recipe` to build up your recipe database
4. Use `/analyze_symptoms` (when you have digestive data) to identify food patterns
