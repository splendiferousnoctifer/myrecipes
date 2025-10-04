# My Recipes 🍳

A modern, clean, and intriguing Vue.js recipe viewer that loads recipes from JSON files and displays them in a beautiful, interactive interface.

## Features

- 🎨 **Modern Design**: Clean, gradient-based UI with smooth animations
- 🔍 **Smart Search**: Search by recipe name, description, or tags
- 🏷️ **Filtering**: Filter by category and difficulty level
- 📱 **Responsive**: Works perfectly on desktop, tablet, and mobile
- 🍽️ **Detailed View**: Click any recipe for full details including ingredients, instructions, and nutrition
- ⚡ **Fast Loading**: Optimized performance with lazy loading
- 🎭 **Interactive**: Hover effects, smooth transitions, and engaging animations

## Quick Start

1. **Install dependencies:**
   ```bash
   npm install
   ```

2. **Start development server:**
   ```bash
   npm run dev
   ```

3. **Open your browser:**
   Navigate to `http://localhost:3000`

## Project Structure

```
myrecipes/
├── data/
│   └── recipes.json          # Recipe data in JSON format
├── src/
│   ├── components/
│   │   ├── SearchBar.vue     # Search and filter component
│   │   ├── RecipeCard.vue    # Recipe card display
│   │   └── RecipeModal.vue   # Detailed recipe view
│   ├── App.vue               # Main application component
│   ├── main.js               # Application entry point
│   └── style.css             # Global styles
├── index.html                # HTML template
├── package.json              # Dependencies and scripts
└── vite.config.js            # Vite configuration
```

## Adding New Recipes

To add new recipes:

1. **Create a new JSON file** in the `data/` folder (e.g., `data/newRecipe.json`)
2. **Add the filename** to `data/recipes-manifest.json`
3. **Restart the dev server** to see your new recipe

Each recipe JSON file should follow this structure:

```json
{
  "id": "12345",
  "name": "Recipe Name",
  "description": "Brief description of the recipe",
  "prepTime": "PT0H15M0S",
  "cookTime": "PT0H20M0S",
  "totalTime": "PT0H35M0S",
  "recipeYield": 4,
  "recipeCategory": "Main",
  "keywords": "vegetarian, quick, easy",
  "recipeIngredient": [
    "1 cup ingredient",
    "2 tbsp another ingredient"
  ],
  "recipeInstructions": [
    "First step instructions",
    "Second step instructions"
  ],
  "image": "https://example.com/image.jpg",
  "nutrition": {
    "calories": "320 kcal",
    "proteinContent": "12 g",
    "carbohydrateContent": "35 g",
    "fatContent": "14 g"
  }
}
```

**Note:** Time format uses ISO 8601 duration (PT0H15M0S = 0 hours, 15 minutes, 0 seconds)

### Example: Adding a New Recipe

1. Create `data/myNewRecipe.json` with your recipe data
2. Edit `data/recipes-manifest.json` and add `"myNewRecipe.json"` to the array:
   ```json
   [
     "leekRisotto.json",
     "coconutCurry.json",
     "zucchiniPizza.json",
     "myNewRecipe.json"
   ]
   ```
3. Restart the dev server with `npm run dev`

## Available Scripts

- `npm run dev` - Start development server
- `npm run build` - Build for production
- `npm run preview` - Preview production build

## Technologies Used

- **Vue 3** - Progressive JavaScript framework
- **Vite** - Fast build tool and dev server
- **CSS3** - Modern styling with gradients and animations
- **Font Awesome** - Icon library
- **Google Fonts** - Inter font family

## Customization

The app is fully customizable:

- **Colors**: Modify the gradient colors in `src/style.css`
- **Layout**: Adjust grid layouts and spacing
- **Components**: Customize individual Vue components
- **Data**: Add more recipe fields as needed

Enjoy cooking! 👨‍🍳👩‍🍳