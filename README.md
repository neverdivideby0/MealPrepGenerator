# MealPrepGenerator

**Sunday Prep Box** is a single-page meal-prep planner. Hit **Roll the box** and it picks one protein, two vegetables, one sauce and one carb base. You get the full recipe for each, a merged shopping list scaled to how many boxes you're making, and a warning when something won't last the week.

The recipe library has 67 components from four sources:

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
- **Add your own recipes.** Use the in-page form. Ingredient lines like `600 g chicken thigh, boneless` scale and merge into the shopping list automatically.

## Running it

It's one static file with no build step and no dependencies (fonts load from Google Fonts).

```bash
# open directly
open index.html

# or serve it locally
python3 -m http.server 8000   # then visit http://localhost:8000
```

To host it, enable **GitHub Pages** on this repo (Settings → Pages → deploy from the `main` branch, root folder).

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

## Food-safety notes

Fridge times assume an airtight container at 4°C or colder, with cooked food (especially rice) cooled and refrigerated within an hour. When in doubt, freeze the later boxes on prep day.
