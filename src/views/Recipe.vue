<template>
  <div class="recipe-page">
    <header class="header">
      <div class="container">
        <div class="header-content">
          <button @click="goBack" class="back-button">
            <i class="fas fa-arrow-left"></i>
            Back to Recipes
          </button>
          <h1 class="logo">
            <i class="fas fa-utensils"></i>
            Sam's Recipes
          </h1>
          <div></div> <!-- Spacer for flex layout -->
        </div>
      </div>
    </header>

    <main class="main">
      <div class="container">
        <div v-if="loading" class="loading">
          <div class="spinner"></div>
          <p>Loading recipe...</p>
        </div>

        <div v-else-if="recipe" class="recipe-detail">
          <div class="recipe-hero">
            <img 
              :src="recipe.image" 
              :alt="recipe.name"
              class="recipe-image"
              @error="handleImageError"
            />
            <div class="recipe-info">
              <h1 class="recipe-title">{{ recipe.name }}</h1>
              <p v-if="recipe.description" class="recipe-description">{{ recipe.description }}</p>
              <p v-if="recipe.keywords" class="recipe-keywords">{{ recipe.keywords }}</p>
            </div>
          </div>

          <div class="recipe-meta">
            <div class="meta-grid">
              <div class="meta-item" v-if="recipe.prepTime">
                <i class="fas fa-clock"></i>
                <div>
                  <span class="meta-label">Prep Time</span>
                  <span class="meta-value">{{ formatDuration(recipe.prepTime) }}</span>
                </div>
              </div>
              <div class="meta-item" v-if="recipe.cookTime">
                <i class="fas fa-fire"></i>
                <div>
                  <span class="meta-label">Cook Time</span>
                  <span class="meta-value">{{ formatDuration(recipe.cookTime) }}</span>
                </div>
              </div>
              <div class="meta-item" v-if="recipe.totalTime">
                <i class="fas fa-stopwatch"></i>
                <div>
                  <span class="meta-label">Total Time</span>
                  <span class="meta-value">{{ formatDuration(recipe.totalTime) }}</span>
                </div>
              </div>
              <div class="meta-item" v-if="recipe.recipeCategory">
                <i class="fas fa-tag"></i>
                <div>
                  <span class="meta-label">Category</span>
                  <span class="meta-value">{{ recipe.recipeCategory }}</span>
                </div>
              </div>
            </div>
          </div>

          <div class="recipe-content">
            <div class="content-section" v-if="recipe.recipeIngredient && recipe.recipeIngredient.length">
              <div class="section-header">
                <h2 class="section-title">
                  <i class="fas fa-list"></i>
                  Ingredients
                </h2>
                <div class="serving-controls" v-if="recipe.recipeYield">
                  <span class="serving-label">Servings:</span>
                  <button @click="decreaseServings" class="serving-btn">-</button>
                  <span class="serving-value">{{ currentServings }}</span>
                  <button @click="increaseServings" class="serving-btn">+</button>
                </div>
              </div>
              <ul class="ingredients-list">
                <li v-for="ingredient in adjustedIngredients" :key="ingredient">
                  {{ ingredient }}
                </li>
              </ul>
            </div>

            <div class="content-section" v-if="recipe.recipeInstructions && recipe.recipeInstructions.length">
              <h2 class="section-title">
                <i class="fas fa-clipboard-list"></i>
                Instructions
              </h2>
              <ol class="instructions-list">
                <li v-for="(instruction, index) in recipe.recipeInstructions" :key="index">
                  {{ instruction }}
                </li>
              </ol>
            </div>

            <div class="content-section" v-if="recipe.nutrition">
              <h2 class="section-title">
                <i class="fas fa-chart-pie"></i>
                Nutrition Information
              </h2>
              <div class="nutrition-list">
                <div class="nutrition-row" v-if="recipe.nutrition.calories">
                  <span class="nutrition-label">Calories:</span>
                  <span class="nutrition-value">{{ recipe.nutrition.calories }}</span>
                </div>
                <div class="nutrition-row" v-if="recipe.nutrition.proteinContent">
                  <span class="nutrition-label">Protein:</span>
                  <span class="nutrition-value">{{ recipe.nutrition.proteinContent }}</span>
                </div>
                <div class="nutrition-row" v-if="recipe.nutrition.carbohydrateContent">
                  <span class="nutrition-label">Carbs:</span>
                  <span class="nutrition-value">{{ recipe.nutrition.carbohydrateContent }}</span>
                </div>
                <div class="nutrition-row" v-if="recipe.nutrition.fatContent">
                  <span class="nutrition-label">Fat:</span>
                  <span class="nutrition-value">{{ recipe.nutrition.fatContent }}</span>
                </div>
                <div class="nutrition-row" v-if="recipe.nutrition.saturatedFatContent">
                  <span class="nutrition-label">Saturated Fat:</span>
                  <span class="nutrition-value">{{ recipe.nutrition.saturatedFatContent }}</span>
                </div>
                <div class="nutrition-row" v-if="recipe.nutrition.sugarContent">
                  <span class="nutrition-label">Sugar:</span>
                  <span class="nutrition-value">{{ recipe.nutrition.sugarContent }}</span>
                </div>
              </div>
            </div>
          </div>
        </div>

        <div v-else class="no-recipe">
          <i class="fas fa-exclamation-triangle"></i>
          <h3>Recipe not found</h3>
          <p>The recipe you're looking for doesn't exist.</p>
          <button @click="goBack" class="back-button">Back to Recipes</button>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
import { ref, computed, onMounted } from 'vue'
import { useRouter } from 'vue-router'

export default {
  name: 'Recipe',
  props: {
    id: {
      type: String,
      required: true
    }
  },
  setup(props) {
    const router = useRouter()
    const recipe = ref(null)
    const loading = ref(true)
    const currentServings = ref(2)

    const loadRecipe = async () => {
      try {
        loading.value = true
        
        // Get the base URL
        const baseUrl = import.meta.env.BASE_URL
        
        // Load the manifest of recipe files
        const manifestResponse = await fetch(`${baseUrl}data/recipes-manifest.json`)
        const recipeFiles = await manifestResponse.json()
        
        // Try to load all recipes and find the one with matching ID
        for (const filename of recipeFiles) {
          try {
            const response = await fetch(`${baseUrl}data/${filename}`)
            const data = await response.json()
            
            if (data.id === props.id) {
              recipe.value = data
              currentServings.value = data.recipeYield || 2
              break
            }
          } catch (error) {
            console.error(`Error loading ${filename}:`, error)
          }
        }
        
        // If no matching recipe found, show error
        if (!recipe.value) {
          console.error('Recipe not found with ID:', props.id)
        }
      } catch (error) {
        console.error('Error loading recipe:', error)
      } finally {
        loading.value = false
      }
    }

    const formatDuration = (duration) => {
      if (!duration) return ''
      // Parse ISO 8601 duration format (PT0H35M0S)
      const match = duration.match(/PT(?:(\d+)H)?(?:(\d+)M)?(?:(\d+)S)?/)
      if (!match) return duration
      
      const hours = parseInt(match[1] || '0')
      const minutes = parseInt(match[2] || '0')
      
      if (hours > 0) {
        return `${hours}h ${minutes}m`
      }
      return `${minutes}m`
    }

    const handleImageError = (event) => {
      event.target.src = 'https://images.unsplash.com/photo-1546554137-f86b9593a222?w=800&h=600&fit=crop'
    }

    const goBack = () => {
      router.push('/')
    }

    const increaseServings = () => {
      if (currentServings.value < 20) {
        currentServings.value++
      }
    }

    const decreaseServings = () => {
      if (currentServings.value > 1) {
        currentServings.value--
      }
    }

    const adjustIngredientAmount = (ingredient, originalServings, newServings) => {
      const ratio = newServings / originalServings
      
      // Match numbers in the ingredient string and adjust them
      return ingredient.replace(/(\d+(?:\.\d+)?)\s*([a-zA-Z\u00e4\u00f6\u00fc\u00c4\u00d6\u00dc\u00df]+)/g, (match, amount, unit) => {
        const newAmount = Math.round((parseFloat(amount) * ratio) * 10) / 10
        return `${newAmount} ${unit}`
      })
    }

    const adjustedIngredients = computed(() => {
      if (!recipe.value || !recipe.value.recipeIngredient) return []
      
      const originalServings = recipe.value.recipeYield || 2
      return recipe.value.recipeIngredient.map(ingredient => 
        adjustIngredientAmount(ingredient, originalServings, currentServings.value)
      )
    })

    onMounted(() => {
      console.log('Recipe component mounted, ID:', props.id)
      loadRecipe()
    })

    return {
      recipe,
      loading,
      currentServings,
      adjustedIngredients,
      formatDuration,
      handleImageError,
      goBack,
      increaseServings,
      decreaseServings
    }
  }
}
</script>
