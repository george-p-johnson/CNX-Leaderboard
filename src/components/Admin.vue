<template>
  <div id="container">
    <h2>Admin Panel</h2>

    <div id="header">
       <div class="heading">Name</div>
      <div>Score</div>
    </div>

    <!-- 5 static input rows -->
    <div id="data" v-for="(entry, index) in entries" :key="index" style="margin-bottom: 10px;">
      <input v-model="entry.name" name="Name" placeholder="Name" />
      <input v-model.number="entry.score" name="Score" placeholder="Score" type="number" />
    </div>

    <div id="buttonContainer">
      <button @click="saveEntries">Save to Google Sheet</button>
      <button @click="fetchEntries">Refresh data from Google Sheet</button>
    </div>
  </div>
</template>

<style scoped>
#container {
  margin: 0 auto;
  display: table;
}

h2 {
  text-align: center;
}

#header {
  display: flex
;
    gap: 1rem;
    font-weight: bold;
    margin-bottom: 0.5rem;
    left: 92px;
    position: relative;
}

.heading {
  width: 150px;
}

#data {
  margin: 0 auto;
  display: table;
}

#buttonContainer {
  margin-top: 25px;
}

button {
  padding: 10px 30px;
  margin: 5px 10px;
  border-radius: 50px;
}
</style>

<script setup>
import { ref, onMounted } from 'vue';
import { store } from '../store.js';

const API_URL = 'https://script.google.com/macros/s/AKfycbxsKJvqveL30rKows7CiECYAeyPY2jeC28JLSW7PupuK8gX-wYtqmyEKgNhAmhOfB6P/exec';

// Local entries (fixed 5)
const entries = ref([
  { name: '', score: 0 },
  { name: '', score: 0 },
  { name: '', score: 0 },
  { name: '', score: 0 },
  { name: '', score: 0 },
]);

// Fetch existing data from Google Sheet
const fetchEntries = async () => {
  try {
    const response = await fetch(API_URL);
    const data = await response.json();

    // Fill in existing data into our 5 fixed rows
    for (let i = 0; i < entries.value.length; i++) {
      entries.value[i].name = data[i]?.name || '';
      entries.value[i].score = data[i]?.score || 0;
    }

    // Also update the global store for Leaderboard
    store.entries = [...entries.value];
  } catch (err) {
    console.error(err);
  }
};

// Save to Google Sheet
const saveEntries = async () => {
  try {
    const response = await fetch(API_URL, {
      method: 'POST',
      body: JSON.stringify(entries.value),
    });
    const result = await response.json();
    console.log('Saved:', result);

    // Update the store so Leaderboard reflects changes
    store.entries = [...entries.value];
  } catch (err) {
    console.error(err);
  }
};

onMounted(fetchEntries);
</script>















<!-- https://script.google.com/macros/s/AKfycbxsKJvqveL30rKows7CiECYAeyPY2jeC28JLSW7PupuK8gX-wYtqmyEKgNhAmhOfB6P/exec -->