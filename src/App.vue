<script setup>
import { ref } from 'vue';
const searchQuery = ref('');
const searchResults = ref('');
const isLoading = ref(false);
const error = ref(null);

//Api
const searchWiki = async (query) => {
  const encodedQuery = encodeURIComponent(query);
  const endpoint = `https://en.wikipedia.org/w/api.php?action=query&list=search&prop=info&inprop=url&utf8=&format=json&origin=*&srlimit=10&srsearch=${encodedQuery}`;
  console.log(endpoint)
  try {
    isLoading.value = true;
    const response = await fetch(endpoint);
    const data = await response.json();
    if (data.query && data.query.search) {
      searchResults.value = data.query.search;
      error.value = null;
    }
    else {
      searchResults.value = [];
      error.value = 'No results found';
    }
  } catch (err) {
    console.log("Error fatching data", err);
    searchResults.value = [];
    error.value = 'An error is occurs';
  } finally {
    isLoading.value = false;
  }
}
const submitSearch = () => {
  if (searchQuery.value.trim != '') {
    searchWiki(searchQuery.value);
    console.log(searchWiki)
  }
  else {
    searchResults.value = [];
    error.value = 'please enter the value';
  }
}

</script>

<template>
  <div class="container">
    <form @submit.prevent="submitSearch">
      <input type="text" v-model="searchQuery">
      <button type="submit">Search</button>
    </form>
    <div id="search">
      <div v-if="isLoading">Loading......</div>
      <p v-if="searchResults.length">
      <div v-for="result in searchResults" :key="result.pageid">
        <h2>
          <a :href="`https://en.wikipedia.org/?curid=${result.pageid}`" target="_blank">{{ result.title }}
          </a>
        </h2>
        <a :href="`https://en.wikipedia.org/?curid=${result.pageid}`" class="result-link" target="_blank">{{
          `https://en.wikipedia.org/?curid=${result.pageid}` }}
        </a>
        <p class="result-snippet" v-html="result.snippet"></p>
      </div>
      </p>
    </div>
  </div>
</template> 