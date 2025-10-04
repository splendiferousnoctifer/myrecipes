<template>
  <div class="home">
    <header class="header">
      <div class="container">
        <h1 class="logo">
          <i class="fas fa-utensils"></i>
          My Recipes
        </h1>
        <p class="subtitle">Discover, Cook, and Enjoy</p>
      </div>
    </header>

    <main class="main">
      <div class="container">
        <SearchBar 
          v-model="searchQuery" 
          @filter-change="handleFilterChange"
          :categories="categories"
        />
        
        <div v-if="loading" class="loading">
          <div class="spinner"></div>
          <p>Loading delicious recipes...</p>
        </div>

        <div v-else-if="filteredRecipes.length === 0" class="no-results">
          <i class="fas fa-search"></i>
          <h3>No recipes found</h3>
          <p>Try adjusting your search or filters</p>
        </div>

        <div v-else class="recipes-grid">
          <RecipeCard 
            v-for="recipe in filteredRecipes" 
            :key="recipe.id"
            :recipe="recipe"
            @view-recipe="viewRecipe"
          />
        </div>
      </div>
    </main>
  </div>
</template>

<script>
import { ref, computed, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import SearchBar from '../components/SearchBar.vue'
import RecipeCard from '../components/RecipeCard.vue'

export default {
  name: 'Home',
  components: {
    SearchBar,
    RecipeCard
  },
  setup() {
    const router = useRouter()
    const recipes = ref([])
    const loading = ref(true)
    const searchQuery = ref('')
    const selectedCategory = ref('')

    const categories = ['Main', 'Dessert', 'Appetizer', 'Side', 'Soup', 'Salad']

    const filteredRecipes = computed(() => {
      let filtered = recipes.value

      // Search filter
      if (searchQuery.value) {
        const query = searchQuery.value.toLowerCase()
        filtered = filtered.filter(recipe => 
          recipe.name.toLowerCase().includes(query) ||
          (recipe.description && recipe.description.toLowerCase().includes(query)) ||
          (recipe.keywords && recipe.keywords.toLowerCase().includes(query))
        )
      }

      // Category filter
      if (selectedCategory.value) {
        filtered = filtered.filter(recipe => recipe.recipeCategory === selectedCategory.value)
      }

      return filtered
    })

    const loadRecipes = async () => {
      try {
        loading.value = true
        
        // Load the manifest of recipe files
        const manifestResponse = await fetch('/data/recipes-manifest.json')
        const recipeFiles = await manifestResponse.json()
        
        // Load all recipes
        const recipePromises = recipeFiles.map(async (filename) => {
          try {
            const response = await fetch(`/data/${filename}`)
            return await response.json()
          } catch (error) {
            console.error(`Error loading ${filename}:`, error)
            return null
          }
        })
        
        const loadedRecipes = await Promise.all(recipePromises)
        recipes.value = loadedRecipes.filter(recipe => recipe !== null)
      } catch (error) {
        console.error('Error loading recipes:', error)
      } finally {
        loading.value = false
      }
    }

    const handleFilterChange = (filters) => {
      selectedCategory.value = filters.category
    }

    const viewRecipe = (recipe) => {
      console.log('Viewing recipe:', recipe)
      router.push(`/recipe/${recipe.id}`)
    }

    onMounted(() => {
      console.log('Home component mounted')
      loadRecipes()
    })

    return {
      recipes,
      loading,
      searchQuery,
      selectedCategory,
      categories,
      filteredRecipes,
      handleFilterChange,
      viewRecipe
    }
  }
}
</script>
