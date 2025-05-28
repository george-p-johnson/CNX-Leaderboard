<template>
  <div id="container">
    <h2>Shoot your Shot</h2>
    <ul>
      <li v-for="(entry, index) in topEntries" :key="index">
        {{ index + 1 }}. {{ entry.name }} - {{ entry.score }}
      </li>
    </ul>
  </div>
</template>


<style scoped>
#container {
  margin: 0 auto;
  display: table;
  background-color: #90D0FE;
  height: 1920px;
  width: 1080px;
  background-image: url('/img/bg.png');
}

h2 {
  text-align: center;
  margin: 50px auto;
  font-size: 72px;
}

ul {
  list-style: none;
  padding: 0;
}

li {
  height: 100px;
  width: 500px;
  background-color: #0B5CAB;
  border-radius: 50px;
  color: #ffffff;
  text-align: left;
  text-indent: 125px;
  line-height: 100px; 
  font-size: 32px;
  font-family: "AvantGardeForSalesforce-Demi", sans-serif;
  margin: 25px auto; 
  display: block; 
}
</style>

<script setup>
import { onMounted, onUnmounted, computed } from 'vue';
import { store } from '../store.js';

const API_URL = 'https://script.google.com/macros/s/AKfycbxsKJvqveL30rKows7CiECYAeyPY2jeC28JLSW7PupuK8gX-wYtqmyEKgNhAmhOfB6P/exec';

let intervalId = null;

// Fetch data from Google Sheet
const fetchEntries = async () => {
  try {
    const response = await fetch(API_URL);
    store.entries = await response.json();
  } catch (err) {
    console.error(err);
  }
};

onMounted(() => {
  fetchEntries(); // initial fetch
  intervalId = setInterval(fetchEntries, 5000); // fetch every 5 seconds
});

onUnmounted(() => {
  clearInterval(intervalId);
});

// Computed top 5
const topEntries = computed(() => {
  return [...store.entries].sort((a, b) => b.score - a.score).slice(0, 5);
});
</script>







<!-- const API_URL = 'https://script.google.com/macros/s/AKfycbxsKJvqveL30rKows7CiECYAeyPY2jeC28JLSW7PupuK8gX-wYtqmyEKgNhAmhOfB6P/exec'; -->
