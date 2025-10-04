<template>
  <div class="search-container" ref="searchContainer">
    <div class="search-row">
      <input
        v-model="localSearchQuery"
        type="text"
        placeholder="Search recipes, ingredients, or tags..."
        class="search-input"
        @input="updateSearch"
      />
      
      <button @click="toggleFilterPopup" class="filter-button">
        <i class="fas fa-filter"></i>
        Filters
      </button>
    </div>
    
    <div v-if="showFilterPopup" class="filter-popup">
      <div class="filter-content">
        <div class="filter-group">
          <label class="filter-label">Category</label>
          <select
            v-model="localCategory"
            class="filter-select"
            @change="updateFilters"
          >
            <option value="">All Categories</option>
            <option v-for="category in categories" :key="category" :value="category">
              {{ category }}
            </option>
          </select>
        </div>
        
        <div class="filter-actions">
          <button @click="clearFilters" class="clear-button">
            <i class="fas fa-times"></i>
            Clear
          </button>
          <button @click="closeFilterPopup" class="apply-button">
            Apply
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, watch, onMounted, onUnmounted } from 'vue'

export default {
  name: 'SearchBar',
  props: {
    modelValue: {
      type: String,
      default: ''
    },
    categories: {
      type: Array,
      required: true
    }
  },
  emits: ['update:modelValue', 'filter-change'],
  setup(props, { emit }) {
    const localSearchQuery = ref(props.modelValue)
    const localCategory = ref('')
    const showFilterPopup = ref(false)
    const searchContainer = ref(null)

    const updateSearch = () => {
      emit('update:modelValue', localSearchQuery.value)
    }

    const updateFilters = () => {
      emit('filter-change', {
        category: localCategory.value
      })
    }

    const toggleFilterPopup = () => {
      showFilterPopup.value = !showFilterPopup.value
    }

    const closeFilterPopup = () => {
      showFilterPopup.value = false
    }

    const handleClickOutside = (event) => {
      if (searchContainer.value && !searchContainer.value.contains(event.target)) {
        closeFilterPopup()
      }
    }

    const clearFilters = () => {
      localSearchQuery.value = ''
      localCategory.value = ''
      updateSearch()
      updateFilters()
      closeFilterPopup()
    }

    watch(() => props.modelValue, (newValue) => {
      localSearchQuery.value = newValue
    })

    onMounted(() => {
      document.addEventListener('click', handleClickOutside)
    })

    onUnmounted(() => {
      document.removeEventListener('click', handleClickOutside)
    })

    return {
      localSearchQuery,
      localCategory,
      showFilterPopup,
      searchContainer,
      updateSearch,
      updateFilters,
      toggleFilterPopup,
      closeFilterPopup,
      clearFilters
    }
  }
}
</script>

<style scoped>
.clear-button {
  background: #dc3545;
  color: white;
  border: none;
  padding: 0.75rem 1rem;
  border-radius: 10px;
  font-size: 0.9rem;
  cursor: pointer;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.clear-button:hover {
  background: #c82333;
  transform: translateY(-2px);
}

.clear-button i {
  font-size: 0.8rem;
}
</style>
