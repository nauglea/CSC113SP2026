# Product Requirements Document
## Protein-Focused Vegetarian/Pescatarian Meal Planner — MVP (v1)

---

## 1. Problem Statement

Most people tracking protein intake rely on general-purpose calorie counters (MyFitnessPal, Cronometer) that treat protein as one metric among dozens. These tools are not designed around protein-first meal planning, offer little guidance on building full days from scratch, and provide no curated support for vegetarian or pescatarian diets — which have a narrower set of high-protein staples that require intentional planning to hit daily targets.

There is no lightweight tool purpose-built to help vegetarians and pescatarians consistently hit a daily protein target of **80–100g** across **3 meals and 2 snacks**, without the overhead of full nutrition logging.

---

## 2. Target User

**Primary Persona: The Protein-Curious Plant-Forward Eater**

- Age: 22–38
- Diet: Vegetarian or pescatarian (no red meat or poultry)
- Goal: Hit 80–100g of protein per day to support fitness, body composition, or general health
- Frustration: Unsure which vegetarian/pescatarian foods are protein-dense; struggles to plan a full day that adds up without obsessing over every macro
- Tech comfort: Comfortable with web or mobile apps; does not want to enter nutrition data manually
- Scenario: Plans meals loosely at the start of the day or week, wants a simple tool to check "will this day hit my goal?"

**Secondary Persona: The CS Student / Early Adopter Tester**
- Developer or technically savvy user helping validate the MVP
- Interested in the core planning loop and data correctness over UI polish

---

## 3. Must-Have Features (MVP)

### Feature 1 — Curated Protein Food Database (Vegetarian/Pescatarian)

A pre-built, read-only database of **50–80 common high-protein vegetarian and pescatarian foods**, each with a serving size and protein gram value.

**Includes:**
- Dairy & eggs: Greek yogurt, cottage cheese, eggs, hard cheese
- Legumes: Lentils, black beans, chickpeas, edamame, tofu, tempeh
- Fish & seafood: Salmon, tuna (canned), shrimp, sardines, cod
- Grains & seeds: Quinoa, hemp seeds, chia seeds
- Protein supplements: Whey protein, plant-based protein powder

**Data shape per item:**
```json
{
  "id": "greek_yogurt_plain",
  "name": "Greek Yogurt (plain, 2%)",
  "serving_size": "1 cup (245g)",
  "protein_g": 20,
  "category": "dairy",
  "diet_type": ["vegetarian", "pescatarian"]
}
```

**Acceptance criteria:**
- All items are vegetarian or pescatarian (no beef, chicken, pork)
- Each item has a single canonical serving size with a protein value verified against USDA FoodData Central
- Items are filterable by category and diet type
- No user-submitted food entries in v1

---

### Feature 2 — Daily Meal Plan Builder

A structured daily planner with **5 fixed slots**: Breakfast, Morning Snack, Lunch, Afternoon Snack, Dinner. Users assign one or more foods from the database to each slot.

**Behavior:**
- Each slot displays assigned foods and their protein contribution
- A running **protein total** updates in real time as foods are added or removed
- A **progress indicator** shows proximity to the 80–100g target range
- Visual state changes when the target is met (e.g., green indicator) vs. under (yellow) or over (orange)
- Users can adjust serving multipliers (e.g., 0.5x, 1x, 1.5x, 2x) per food item

**Acceptance criteria:**
- All 5 meal slots are always visible
- Protein total recalculates instantly on any change
- Target range (80–100g) is hardcoded for v1 (not user-configurable)
- Plan resets to empty on page refresh (no persistence in v1)

---

### Feature 3 — Protein Gap Suggestions

When the current plan is **below 80g**, the app surfaces **3 quick-add suggestions** to close the gap — prioritizing high-protein-per-calorie foods that fit naturally as snacks or meal add-ons.

**Logic:**
- Calculate remaining protein needed: `gap = 80 - current_total`
- Filter database for foods where `protein_g >= gap * 0.3` (meaningful contribution)
- Rank by `protein_g / serving_weight_g` (protein density)
- Return top 3, skipping foods already in the plan

**Display:**
- Shown in a "Close the Gap" panel below the daily total
- Each suggestion shows: food name, serving size, protein grams, and a one-click "Add to [slot]" action defaulting to the most appropriate slot (e.g., snack slots first)

**Acceptance criteria:**
- Suggestions update whenever the plan changes
- Panel is hidden when total >= 80g
- "Add" action correctly inserts the item into the specified slot and updates the total

---

## 4. User Interaction Flow

```
[App Load]
     |
     v
[Empty Day View]
  5 meal slots visible, all empty
  Protein total: 0g / 80–100g target
     |
     v
[User clicks "+ Add Food" on a slot]
     |
     v
[Food Search/Browse Panel opens]
  - Search by name
  - Filter by category (dairy, legumes, fish, grains)
  - Each result shows: name, serving, protein grams
     |
     v
[User selects a food item]
     |
     v
[Item added to slot]
  - Slot updates with item name + protein
  - Running total updates
  - Serving multiplier defaulted to 1x (adjustable)
     |
     v
[Protein Gap Panel evaluates total]
  IF total < 80g:
    → Show top 3 "Close the Gap" suggestions
  IF 80g ≤ total ≤ 100g:
    → Show "Goal Met" state, hide gap panel
  IF total > 100g:
    → Show "Over Target" indicator (no error, informational only)
     |
     v
[User repeats for remaining slots]
     |
     v
[Completed Day]
  All 5 slots filled, total in 80–100g range
  Summary: per-slot protein breakdown visible
```

---

## 5. What to Exclude from v1

The following are explicitly **out of scope** for the MVP to maintain build speed and focus.

| Excluded Feature | Reason / Future Version |
|---|---|
| User accounts & authentication | No persistence needed for v1; adds backend complexity |
| Saving / loading meal plans | Deferred to v2 with local storage or user accounts |
| Full macro tracking (carbs, fat, calories) | Out of scope; protein-only focus is the core value prop |
| Custom food entry by users | Data integrity risk; curated DB is sufficient for validation |
| Weekly or multi-day planning | Adds UI complexity; single-day loop validates the core concept |
| Dietary restriction filters beyond veg/pescatarian | Gluten-free, nut-free, etc. are v2+ |
| Recipe suggestions or meal combos | Separate product surface; out of scope for food-item-based MVP |
| Mobile native app (iOS/Android) | Web-first; responsive design sufficient for v1 |
| API integrations (Spoonacular, Edamam, etc.) | Curated static DB avoids rate limits and costs during validation |
| Notifications or reminders | Requires accounts and push infrastructure |
| Social or sharing features | Premature for MVP |

---

## 6. Technical Notes for Code Generation

- **Frontend:** React (functional components + hooks) or plain HTML/JS — both viable for v1
- **State management:** Local component state (`useState`) is sufficient; no Redux/Zustand needed
- **Data layer:** JSON file for food database, imported statically (no backend required for v1)
- **Protein calculation:** Pure functions, easily unit-testable
- **Styling:** Tailwind CSS recommended for speed
- **No backend required for v1** — all logic runs client-side
- **Key data structures to define first:**
  - `FoodItem` — id, name, serving_size, protein_g, category, diet_type
  - `MealSlot` — id, label, items: `FoodItem[]`, multipliers: `Record<id, number>`
  - `DayPlan` — slots: `MealSlot[5]`, total_protein: number

---

*Document version: 1.0 | Status: Ready for development | Scope: MVP / v1*
