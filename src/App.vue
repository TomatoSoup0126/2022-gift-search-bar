<script setup>
import { ref } from 'vue'
import { watchDebounced } from '@vueuse/core'

const searchTerm = ref('')
const isLoading = ref(false)
const gifts = ref([])

const findProducts = async term => {
  try {
    isLoading.value = true
    const response = await fetch(`https://dummyjson.com/products/search?q=${term}`)
    const data = await response.json()
    console.log(data.products)
    if (data.products) {
      gifts.value = data.products
      if (data.products.length === 0) {
        alert('no results')
      }
    }
  } catch (error) {
    console.error(error)
  } finally {
    isLoading.value = false
  }
}

const resetInput = () => {
  searchTerm.value = ''
  gifts.value = []
}

watchDebounced(
  searchTerm,
  (newTerm) => { 
    if (newTerm !== '' ) {
      console.log('search')
      findProducts(newTerm)
    } else {
      console.log('empty')
      resetInput()
    }
  },
  { debounce: 300 },
)
</script>

<template>
  <div class="w-full h-full flex flex-col gap-5 justify-center items-center">
    <h1 class="text-4xl font-bold">Gift Search Bar</h1>
    <input
      type="text"
      class="p-2 border-2 border-gray-dark"
      v-model="searchTerm"
      placeholder="Start typing..."
      @focus="resetInput"
    />
    <template v-if="isLoading">Searching...</template>
    <template v-else>
      <ul v-if="gifts.length > 0" class="list-disc">
        <li v-for="item in gifts" :key="item.id">
          {{ `${item.title} - $${item.price}` }}
        </li>
      </ul>
    </template>
  </div>
</template>
