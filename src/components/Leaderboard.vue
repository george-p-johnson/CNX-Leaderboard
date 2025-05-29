<template>
  <div id="container">
    <h2>Shoot your Shot</h2>
    
    <!-- Donations Display -->
    <div class="donations-display">
      <h3>Total Donations Raised</h3>
      <div class="donation-amount">${{ formattedDonation }}</div>
    </div>
    
    <!-- Leaderboard -->
    <ul>
      <li v-for="(entry, index) in topEntries" :key="index">
        {{ index + 1 }}. {{ entry.name }} - {{ entry.score }}
      </li>
    </ul>

    <!-- <div id="overlay"></div> -->
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

/* #container::before {
    content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
} */

h2 {
  text-align: center;
  margin: 80px auto;
  font-size: 100px;
  font-family: "AvantGardeForSalesforce-Demi", sans-serif;
  color: #ffffff;
}

.donations-display {
  text-align: center;
  margin: 100px auto 200px auto;
  /* background-color: rgba(13, 157, 218, 0.9); */
  border-radius: 20px;
  padding: 60px;
  width: 600px;
  background-color: #ffffff;
  color: #00A1E0;

}

.donations-display h3 {
  color: #00A1E0;
  font-size: 36px;
  margin: 0 0 15px 0;
  font-family: "AvantGardeForSalesforce-Demi", sans-serif;
}

.donation-amount {
  color: #00A1E0;
  font-size: 48px;
  font-weight: bold;
  font-family: "AvantGardeForSalesforce-Demi", sans-serif;
}

ul {
  list-style: none;
  padding: 0;
}

li {
  height: 100px;
  width: 500px;
  background-color: #ffffff;
  border-radius: 50px;
  color: #00A1E0;
  text-align: left;
  text-indent: 125px;
  line-height: 100px; 
  font-size: 32px;
  font-family: "AvantGardeForSalesforce-Demi", sans-serif;
  margin: 35px auto; 
  display: block; 
}
</style>

<script setup>
import { onMounted, onUnmounted, computed } from 'vue';
import { store } from '../store.js';

const LEADERBOARD_API_URL = 'https://script.google.com/macros/s/AKfycbxsKJvqveL30rKows7CiECYAeyPY2jeC28JLSW7PupuK8gX-wYtqmyEKgNhAmhOfB6P/exec';
const DONATIONS_API_URL = 'https://script.google.com/macros/s/AKfycbxu6UrZQo5_w4_PE6AlGrthJruSzlyhJ-8FHLNFBmw30Q4xC5cgzTr92ZYzocqVFZKRcw/exec';

let intervalId = null;

// Fetch leaderboard data from Google Sheet
const fetchEntries = async () => {
  try {
    const response = await fetch(LEADERBOARD_API_URL);
    store.entries = await response.json();
  } catch (err) {
    console.error('Error fetching entries:', err);
  }
};

// Fetch donations data
const fetchDonations = async () => {
  try {
    const response = await fetch(DONATIONS_API_URL);
    const data = await response.json();
    store.donation = data.donation || 0;
  } catch (err) {
    console.error('Error fetching donations:', err);
  }
};

// Combined fetch function
const fetchAll = async () => {
  await Promise.all([fetchEntries(), fetchDonations()]);
};

onMounted(() => {
  fetchAll(); // initial fetch
  intervalId = setInterval(fetchAll, 5000); // fetch every 5 seconds
});

onUnmounted(() => {
  clearInterval(intervalId);
});

// Computed top 5
const topEntries = computed(() => {
  return [...store.entries].sort((a, b) => b.score - a.score).slice(0, 5);
});

// Format donation amount with commas and 2 decimal places
const formattedDonation = computed(() => {
  return (store.donation || 0).toLocaleString('en-US', {
    minimumFractionDigits: 2,
    maximumFractionDigits: 2
  });
});
</script>









<!-- Leaderboard - https://script.google.com/macros/s/AKfycbxsKJvqveL30rKows7CiECYAeyPY2jeC28JLSW7PupuK8gX-wYtqmyEKgNhAmhOfB6P/exec -->

<!-- Donations - https://script.google.com/macros/s/AKfycbxu6UrZQo5_w4_PE6AlGrthJruSzlyhJ-8FHLNFBmw30Q4xC5cgzTr92ZYzocqVFZKRcw/exec -->