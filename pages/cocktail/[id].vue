<template>
  <div class="container mx-auto p-4">
    <NuxtLink to="/" class="text-blue-500 mb-4 inline-block hover:underline">
      ← Retour à la recherche
    </NuxtLink>

    <div v-if="loading" class="text-center">
      Chargement...
    </div>

    <div v-else-if="cocktail" class="max-w-4xl mx-auto">
      <div class="grid grid-cols-1 md:grid-cols-2 gap-8">
        <img :src="cocktail.strDrinkThumb" :alt="cocktail.strDrink" class="rounded-lg w-full">
        
        <div>
          <h1 class="text-3xl font-bold mb-4">{{ cocktail.strDrink }}</h1>
          
          <div class="mb-6">
            <h2 class="text-xl font-semibold mb-2">Catégorie</h2>
            <p>{{ cocktail.strCategory }}</p>
          </div>

          <div class="mb-6">
            <h2 class="text-xl font-semibold mb-2">Ingrédients</h2>
            <ul class="list-disc list-inside">
              <li v-for="ingredient in ingredients" :key="ingredient">
                {{ ingredient }}
              </li>
            </ul>
          </div>

          <div class="mb-6">
            <h2 class="text-xl font-semibold mb-2">Instructions</h2>
            <p class="whitespace-pre-line">{{ cocktail.strInstructions }}</p>
          </div>

          <div class="mb-6">
            <h2 class="text-xl font-semibold mb-2">Verre recommandé</h2>
            <p>{{ cocktail.strGlass }}</p>
          </div>
        </div>
      </div>
    </div>

    <div v-else class="text-center">
      Cocktail non trouvé
    </div>
  </div>
</template>

<script setup>
const route = useRoute()
const cocktail = ref(null)
const loading = ref(true)

const ingredients = computed(() => {
  if (!cocktail.value) return []
  
  const ingredients = []
  for (let i = 1; i <= 15; i++) {
    const ingredient = cocktail.value[`strIngredient${i}`]
    const measure = cocktail.value[`strMeasure${i}`]
    
    if (ingredient) {
      ingredients.push(`${measure || ''} ${ingredient}`.trim())
    }
  }
  return ingredients
})

onMounted(async () => {
  try {
    const response = await fetch(`https://www.thecocktaildb.com/api/json/v1/1/lookup.php?i=${route.params.id}`)
    const data = await response.json()
    cocktail.value = data.drinks?.[0] || null
  } catch (error) {
    console.error('Erreur lors du chargement du cocktail:', error)
    cocktail.value = null
  } finally {
    loading.value = false
  }
})
</script> 