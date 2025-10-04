<template>
  <div class="recipe-card" @click="$emit('view-recipe', recipe)">
    <img 
      :src="recipe.image" 
      :alt="recipe.name"
      class="recipe-image"
      @error="handleImageError"
    />
    
    <div class="recipe-content">
      <div class="recipe-header">
        <div>
          <h3 class="recipe-title">{{ recipe.name }}</h3>
        </div>
        <span class="recipe-category">{{ recipe.recipeCategory }}</span>
      </div>
      
      <p class="recipe-description" v-if="recipe.description">{{ recipe.description }}</p>
      <p class="recipe-keywords" v-if="recipe.keywords">{{ recipe.keywords }}</p>
      
      <div class="recipe-meta">
        <div class="meta-item" v-if="recipe.totalTime">
          <i class="fas fa-clock"></i>
          <span>{{ formatDuration(recipe.totalTime) }}</span>
        </div>
        <div class="meta-item" v-if="recipe.recipeYield">
          <i class="fas fa-users"></i>
          <span>{{ recipe.recipeYield }} servings</span>
        </div>
        <div class="meta-item" v-if="recipe.nutrition && recipe.nutrition.calories">
          <i class="fas fa-fire"></i>
          <span>{{ recipe.nutrition.calories }}</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'RecipeCard',
  props: {
    recipe: {
      type: Object,
      required: true
    }
  },
  emits: ['view-recipe'],
  setup() {
    const handleImageError = (event) => {
      event.target.src = 'https://images.unsplash.com/photo-1546554137-f86b9593a222?w=500&h=400&fit=crop'
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

    return {
      handleImageError,
      formatDuration
    }
  }
}
</script>
