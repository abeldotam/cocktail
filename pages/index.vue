<template>
  <div class="container mx-auto p-4">
    <h1 class="text-3xl font-bold mb-6">Recherche de Cocktails</h1>
    
    <div class="mb-6">
      <input 
        v-model="searchQuery" 
        @input="searchCocktails"
        type="text" 
        placeholder="Rechercher un cocktail..."
        class="w-full p-2 border rounded-lg"
      />
    </div>

    <div v-if="loading" class="text-center">
      Chargement...
    </div>

    <div v-else-if="cocktails.length" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
      <NuxtLink 
        v-for="cocktail in cocktails" 
        :key="cocktail.idDrink"
        :to="`/cocktail/${cocktail.idDrink}`"
        class="border rounded-lg p-4 hover:shadow-lg transition-shadow"
      >
        <img :src="cocktail.strDrinkThumb" :alt="cocktail.strDrink" class="w-full rounded-lg mb-2">
        <h2 class="text-xl font-bold">{{ cocktail.strDrink }}</h2>
      </NuxtLink>
    </div>

    <div v-else-if="searchQuery" class="text-center">
      Aucun cocktail trouvé
    </div>
  </div>
</template>

<script setup>
const searchQuery = ref('')
const cocktails = ref([])
const loading = ref(false)

const searchCocktails = useDebounceFn(async () => {
  if (!searchQuery.value) {
    cocktails.value = []
    return
  }

  loading.value = true
  try {
    const response = await fetch(`https://www.thecocktaildb.com/api/json/v1/1/search.php?s=${searchQuery.value}`)
    const data = await response.json()
    cocktails.value = data.drinks || []
  } catch (error) {
    console.error('Erreur lors de la recherche:', error)
    cocktails.value = []
  } finally {
    loading.value = false
  }
}, 300)
</script> 