<script setup>
import { ref, computed } from 'vue';
import { useQuery } from '@vue/apollo-composable';
import { GET_COUNTRIES } from './graphql/queries';

const searchText = ref('')

const { result, loading, error } = useQuery(GET_COUNTRIES);

const filteredCountries = computed(() => {
  const countries = result.value?.countries || []
  if (!searchText.value) return countries
  return countries.filter(c => 
    c.name.toLowerCase().includes(searchText.value.toLowerCase())
  )
})

const clearInputText = () => {
  searchText.value = ""; 
}

</script>

<template>
  <div>
    <h1>🌍 Country Browser</h1>

    <div v-if="loading">Loading...</div>
    <div v-else-if="error">Error: {{ error.message }}</div>

    <div v-else>
      <h2>Filtering options:</h2>
      <label for="filter-search">By country name</label>
      <input type="search" id="filter-search" v-model="searchText"></input>
      <button type="button" @click="clearInputText">Clear</button>
      <div v-if="!filteredCountries.length">No results</div>
      <ul v-else>
        <li v-for="country in filteredCountries" :key="country.code">
          {{ country.name }} {{ country.emoji }} - ({{ country.continent.name }})
        </li>
      </ul>
    </div>
  </div>
</template>
