# MealPrepGenerator

**Sunday Prep Box** is a single-page meal planner with two tabs:

- **Meal prep.** Hit **Roll the box** and it picks one protein, two vegetables, one sauce and one carb base. You get the full recipe for each, a merged shopping list scaled to how many boxes you're making, and a warning when something won't last the week.
- **Weekend dinners.** Recipes you've made or want to try, which aren't meant for meal prep. Roll a main, or choose one, and it suggests a sauce and two sides that go with it (for example, steak with black pepper sauce, mash and creamed spinach).

The meal-prep library has 67 components from four sources:

| Source | What's in it |
| --- | --- |
| Korean banchan | Namul, muchim, jorim, quick kimchi, multigrain rice, japchae, three sauces |
| SaladStop!-style | Components of signature salads and warm bowls (Sabai Sabai, Yeobo Yeobo, Kokoro, Inhale Ex-Kale, Hail Caesar, Tuna San, Go Geisha) |
| Stuff'd-style | Kebab, burrito and bowl fillings plus the sauce lineup (habanero, mayo cucumber, roasted sesame, BBQ mayo, honey mustard) |
| Dabba Street-style | Butter chicken, tikka, paneer, chana masala, chutneys, tzatziki, beetroot hummus, biryani rice, roti, pita |

The restaurant recipes are home versions written from public menu descriptions. They are not the restaurants' own recipes, and the restaurant names are used only to say what inspired each one.

## Features

- **Randomizer with cuisine control.** "Surprise me" keeps each roll inside one cuisine so the box tastes coherent. You can also pin a cuisine or pick "Mix all four".
- **Lock and swap.** Lock the slots you like, then re-roll the rest, or swap a single slot.
- **Prep sheet.** Recipes are listed longest job first, so marinades and rice start while you chop. Amounts scale to your box count (1 to 10); batch recipes like kimchi and hot sauce don't scale.
- **Shopping list.** Ingredients are merged across the box, split into "Buy" and "Check the pantry", with tick boxes and a copy button. Whole items round up to what you'd actually buy.
- **Fridge life and freezer notes.** Each recipe lists how many days it keeps, whether it freezes and how to reheat. The box shows its eat-by date and warns when later boxes would outlast a component.
- **Rough macros.** Approximate kcal and protein per portion, summed per box. These are ballpark figures, not lab values.
- **Recipe library.** Search, filter by cuisine, slot, vegetarian, spicy, freezes, 30 minutes or less, keeps 5+ days. Open any recipe and put it straight into your box.
- **Add your own recipes.** Use the in-page form, for either tab. Ingredient lines like `600 g chicken thigh, boneless` scale and merge into the shopping list automatically.
- **Generate with AI** (claude.ai version only). In the add form, type a dish name with notes ("mala xiang guo, less numbing, for 2") or paste a recipe you found. Claude fills in the form for you to check before you save:
  - ingredients, steps, servings and times;
  - category and tags;
  - for meal prep: fridge life, freezer and reheat notes, and rough macros;
  - for weekend mains: which of your sauces and sides go with the dish.

  **Suggest ideas** proposes dishes you don't have yet. Pick one and it gets written up.

  It uses the page's built-in connection to Claude, so it needs no API key. Calls count against your own Claude usage, and Claude asks your permission on first use. Nothing is saved until you press Save.

### Weekend dinners

33 dishes in four courses:

| Course | Dishes |
| --- | --- |
| Mains | Pan-seared steak, miso salmon, tacos, Pepper Lunch, poke bowl, smashed burgers, mac and cheese, gyudon, beef steak don, shepherd's pie, bolognese, aglio olio, carbonara, pesto pasta |
| Soups & stews | ABC soup, pork lotus root soup, beef stew, tomato soup, mushroom soup, Japanese curry |
| Sides | Korean spinach, mashed potatoes, baked potato, creamed spinach, roast vegetables, pumpkin rice, Japanese marinated eggs, build-a-salad |
| Sauces | Brown, black pepper, red wine, mushroom cream, chimichurri |

- **Dinner picker.** Filter by cuisine, roll or choose a main, then lock or swap the sauce and sides. Mains that carry their own sauce (curry, carbonara, gyudon) say so instead of forcing one. Beef stew and Japanese curry can be rolled as mains.
- **Cook it sheet.** A merged shopping list and every recipe for the dinner, scaled to your servings and ordered longest job first.
- **Your notes.** Your own comments on each dish appear as "Your notes", separate from the recipe's tips.
- **Course and cuisine** are separate filters (Western, Italian, Japanese, Korean, Singaporean, Mexican, Hawaiian, Latin American).

## Running it

It's one static file with no build step and no dependencies (fonts load from Google Fonts).

```bash
# open directly
open index.html

# or serve it locally
python3 -m http.server 8000   # then visit http://localhost:8000
```

To host it, enable **GitHub Pages** on this repo (Settings → Pages → deploy from the `main` branch, root folder).

Generate with AI is hidden on GitHub Pages and local copies, because it relies on the claude.ai page's Claude connection. Everything else works the same.

### Where your own recipes are saved

- **Opened from this repo / GitHub Pages:** recipes you add are saved in that browser's local storage. They don't sync between devices, and clearing site data removes them.
- **The claude.ai version of the page:** recipes are saved in the page's database, so they sync across your devices when you're signed in.

## Adding recipes in code

Curated recipes live in the `CURATED` array in `index.html`. Each entry looks like this:

```js
{id:"bulgogi", src:"saladstop", role:"protein", name:"Beef bulgogi", insp:"Yeobo Yeobo",
 blurb:"One-line description.",
 serves:4, prep:15, cook:10, wait:[30,"marinate"],   // minutes; wait is optional hands-off time
 fridge:4, freeze:"Raw in the marinade, up to 3 months", reheat:"Microwave 1 minute.",
 kcal:300, p:28, tags:["beef"],                       // tags: veg, spicy, seafood, beef, pork
 ing:["500 g thinly sliced beef", "4 tbsp soy sauce", "Salt, to taste"],
 steps:["Step one.", "Step two."],
 tip:"Optional tip."}
```

- `src` is one of `banchan`, `saladstop`, `stuffd`, `dabba`, `mine`.
- `role` is one of `protein`, `veg`, `sauce`, `carb`.
- `ko` (optional) holds the Korean name line for banchan.
- `batch: true` marks recipes made in a fixed batch (kimchi, hot sauce) that shouldn't scale with box count.
- Ingredient lines start with an amount, then an optional unit (`g`, `kg`, `ml`, `l`, `tsp`, `tbsp`, `cup`, `clove`, `bunch`, `small bunch`, `can`, `stalk`, `thumb`, `head`, `slice`, `rasher`), then the item. Anything after the first comma is a prep note and doesn't affect shopping-list merging.

Each cuisine needs at least one protein, two vegetables, one sauce and one carb for "Surprise me" to roll it.

Weekend dishes live in the `WEEKEND` array and use `course` (`main`, `soup`, `side`, `sauce`) and `cuisine` instead of `src` and `role`. Extra fields:

- `notes`: your own comments, shown as "Your notes".
- `pairs: {sauces:[...], sides:[...]}`: what a main goes with, by id. An empty `sauces` list means no sauce is needed; `sauceNote` explains why.
- `asMain: true` lets a soup or stew be rolled as a main. `alsoSide: true` lets a main (mac and cheese) be offered as a side.
- `lists`: grouped suggestions, as in Build-a-Salad.

## Food-safety notes

Fridge times assume an airtight container at 4°C or colder, with cooked food (especially rice) cooled and refrigerated within an hour. When in doubt, freeze the later boxes on prep day.
