<template>
  <div>
    <h1>Welcome to the Wheel View!</h1>
    <p>This is a simple Vue component.</p>
    <button @click="geocode">Geocode!</button>
    <input type="text" v-model="address" placeholder="Enter address">
    <Roulette ref="wheel" :items="items" @click="launchWheel" />
  </div>
</template>

<script setup lang="ts">

import { ref } from 'vue'
import { Roulette } from 'vue3-roulette'

const address = ref('')

const geocode = async () => {
  console.log('Geocoding...')
  const response = await fetch(`https://wheel-of-proxy.niftyoctopus.workers.dev/geolocation?address=${encodeURIComponent(address.value)}`)
  const data = await response.json()
  console.log(data.results[0].geometry.location)
}


// Make a call to get places
// This will again to to the proxy server but with the /places endpoint
// The request format should be for the google nearby places API (new)


const wheel = ref(null);

const items = [
  { id: 1, name: "Banana", htmlContent: "Banana", textColor: "black", background: "" },
  { id: 2, name: "Apple", htmlContent: "Apple", textColor: "black", background: "" },
  { id: 3, name: "Orange", htmlContent: "Orange", textColor: "black", background: "" },
  { id: 4, name: "Cherry", htmlContent: "Cherry", textColor: "black", background: "" },
];

function launchWheel (){
  wheel.value.launchWheel();
}

</script>

<style scoped>

</style>