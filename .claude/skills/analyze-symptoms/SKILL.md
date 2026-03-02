---
name: analyze-symptoms
description: >
  **Digestive Symptom Analyzer**: Correlates Bristol scale and symptom log entries with meals consumed in the preceding 24-48 hours to identify potential food triggers and patterns. Use this skill whenever the user wants to investigate digestive patterns, identify food triggers, analyze their symptom log, check correlations between meals and gut health, review their Bristol scale data, or look for patterns in eczema/ear symptoms alongside food intake. Also trigger when the user says "what's causing my symptoms", "analyze my gut health", "which foods are triggering me", "symptom patterns", "Bristol scale analysis", or mentions investigating food sensitivities.
---

# Analyze Symptoms

You help the user investigate correlations between their food intake and symptoms (primarily digestive, but also eczema and ear issues). This is a hypothesis-generating exercise — you identify patterns and suggest tests, not diagnoses.

## Where to Find Things

- **Symptom log**: `memory/symptom_log.md` — Bristol scale entries with timing, eczema scores, ear scores, and meals in preceding 24-48 hours
- **Meal plans**: `memory/weeks/[MMDDYY]/meals.md` — What was actually eaten each week
- **Recipes**: `memory/recipes/*.md` — Full ingredient lists to break meals down to individual ingredients
- **Components**: `memory/components/` — Nutritional data for individual foods
- **FODMAP plan**: `memory/fodmap_plan.md` — Current elimination/reintroduction protocol and known tolerances
- **Nutritional targets**: `memory/nutritional_targets.md` — May inform whether deficiencies contribute to symptoms

## How Symptom Correlation Works

Digestive symptoms typically appear 24-48 hours after consuming a trigger food, though some reactions (especially to FODMAPs) can be faster (6-24 hours). For each symptom entry, look at all meals eaten in the preceding 48 hours.

The user tracks three symptom categories:
1. **Bristol Scale (1-7)**: Gut health. 3-4 is ideal. 5-7 suggests diarrhea-type issues (IBS-D pattern). 1-2 suggests constipation.
2. **Eczema (0-3)**: Skin inflammation. 0 is clear, 3 is severe. Location matters.
3. **Ears (0-3)**: Itchiness, fullness, irritation. 0 is fine, 3 is infected.

## Workflow

### 1. Read and Parse the Symptom Log

Read `memory/symptom_log.md` and extract all entries. For each entry, note:
- Date and time
- Bristol scale score
- Eczema score and location
- Ear score
- Meals listed in the preceding 24-48 hours
- Any contextual notes (stress, sleep, etc.)

If the log has few entries (less than 2 weeks), tell the user that more data will improve the analysis and show what patterns you can see so far.

### 2. Cross-Reference with Meal Plans

The symptom log should list meals consumed, but cross-reference with `memory/weeks/*/meals.md` to fill in gaps or verify. Break each meal down to its individual ingredients using recipe files.

### 3. Build an Ingredient Frequency Table

For each symptom event (Bristol > 4, eczema > 0, ears > 0), list every ingredient consumed in the 48 hours prior. Then count how often each ingredient appears before symptom events vs. symptom-free periods.

Present this as a table:

| Ingredient | Times Before Symptoms | Times in Symptom-Free Periods | Correlation Score |
|---|---|---|---|

The correlation score is a simple ratio — higher means the ingredient appears disproportionately before symptoms. This isn't statistical proof, but it highlights candidates for elimination testing.

### 4. Look for Patterns

Beyond individual ingredients, look for:
- **Timing patterns**: Do symptoms cluster on certain days of the week? After certain meal types (heavy vs. light)?
- **Combination effects**: Do symptoms appear when two specific ingredients are eaten together but not separately?
- **Dose effects**: Do larger portions of a food correlate more strongly than small amounts?
- **FODMAP categories**: If the user is doing FODMAP testing, group ingredients by FODMAP type (fructans, GOS, polyols, etc.) and check if an entire category correlates
- **Non-food factors**: Note if stress, poor sleep, or other contextual factors appear alongside symptoms
- **Cross-symptom patterns**: Do gut symptoms, eczema, and ear issues flare together (suggesting systemic inflammation) or independently?

### 5. Present Findings

Structure the analysis as:

1. **Summary**: How many entries analyzed, date range, overall symptom frequency
2. **Top Correlations**: The 3-5 ingredients or food categories with strongest correlation to symptoms
3. **Pattern Observations**: Timing patterns, combination effects, non-food factors
4. **FODMAP Alignment**: How findings relate to known FODMAP categories (reference `fodmap_plan.md`)
5. **Recommended Next Steps**: Specific elimination tests to run, ordered by strength of evidence
6. **Confidence Assessment**: How much data supports these observations and what would strengthen the analysis

### 6. Important Boundaries

- Frame everything as "the data suggests" or "this pattern is worth investigating," never "X is causing your symptoms"
- Don't diagnose conditions. Suggest seeing a healthcare provider for clinical concerns
- Distinguish between observations (what the data shows) and hypotheses (what you'd test next)
- If the data is sparse, say so clearly rather than over-interpreting
- Acknowledge confounders — stress, sleep, and other non-food factors can drive symptoms too
