<script setup lang="ts">
  import { ref, onMounted } from 'vue';
  import moment from 'moment';

  const loading = ref(true)
  const title = ref('Global')
  const date = ref('') 
  const stats = ref({})
  const countries = ref([])
  const selectedCountry = ref(0)
  
  function selectCountry() {
    const country = countries.value.find((item) => item.ID === selectedCountry.value);
    selectedCountry.value = country.ID
    
    stats.value = country
    title.value = country.Country
  } 
  
  async function deSelectCountry() {
    selectedCountry.value = 0
    const data = await getData()
    date.value = data.Date
    stats.value = data.Global
    countries.value = data.Countries
    loading.value = false;
  } 

  async function getData() {
    const response = await fetch('https://api.covid19api.com/summary')
    const data = await response.json()
    return data
  }

  function formatNumber(number: any) {
    return number.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ',')
  }

  onMounted(async () => {
    const data = await getData()
    date.value = data.Date
    stats.value = data.Global
    countries.value = data.Countries
    loading.value = false;      
  })
</script>

<template>
  <main v-if="loading" class="flex flex-col align-center justify-center text-center">
    <div class="text-gray-500 text-3xl mt-10 mb-6">
      Fetching Data
    </div>
    <img src="../assets/loading.gif" class="w-64 m-auto" alt=""/>
  </main>
  <main v-else>
    <!-- Title  -->
    <div class="text-center">
      <h2 class="text-3xl font-bold">
        {{ title }}
      </h2>
      <div class="text-2xl mt-4 mb-10">
        {{ moment(date).format('MMMM Do YYYY, h:mm:ss a') }}
      </div>
    </div>

    <!-- Country -->
    <div class="mb-10 flex items-center border-b border-blue-200 py-2">
      <select @change="selectCountry()" v-model="selectedCountry" class="appearance-none bg-transparent border-none w-full text-gray-700 mr-3 py-1 px-2 leading-tight focus:outline-none">
        <option value="0">Select Country</option>
        <option v-for="country in countries" :value="country.ID">
          {{ country.Country }}
        </option>
      </select>
      <button @click="deSelectCountry()" v-if="stats.Country" class="bg-red-500 hover:bg-red-700 border-red-500 hover:border-red-700 text-sm border-4 text-white py-1 px-2 rounded" type="button">
        Clear
      </button>
    </div>
    
    
    <!-- Cards  -->
    <div class="grid md:grid-cols-2 gap-4">

      <!-- Cases -->
      <div class="shadow-md bg-blue-100 p-10 text-center rounded">
        <h3 class="text-3xl text-blue-900 font-bold mb-4">
          Cases
        </h3>
        <div class="text-2xl mb-4">
          <span class="font-bold">New:</span>
          {{ formatNumber(stats.NewConfirmed) }}
        </div>
        <div class="text-2xl mb-4">
          <span class="font-bold">Total:</span>
          {{ formatNumber(stats.TotalConfirmed) }}
        </div>
      </div>

      <!-- Deaths -->
      <div class="shadow-md bg-red-100 p-10 text-center rounded">
        <h3 class="text-3xl text-blue-900 font-bold mb-4">
          Deaths
        </h3>
        <div class="text-2xl mb-4">
          <span class="font-bold">New:</span>
          {{ formatNumber(stats.NewDeaths) }}
        </div>
        <div class="text-2xl mb-4">
          <span class="font-bold">Total:</span>
          {{ formatNumber(stats.TotalDeaths) }}
        </div>
      </div>
    </div>

  </main>
  
</template>
