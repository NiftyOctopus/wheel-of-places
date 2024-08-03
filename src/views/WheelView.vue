<template>
  <div>
    <h1>Welcome to the Wheel View!</h1>
    <p>This is a simple Vue component.</p>
    <button @click="geocode">Geocode!</button>
    <input type="text" v-model="address" placeholder="Enter address">
  </div>
</template>

<script setup lang="ts">

import { ref } from 'vue'

const address = ref('')

const geocode = async () => {
  console.log('Geocoding...')
  const response = await fetch(`https://wheel-of-proxy.niftyoctopus.workers.dev/geolocation?address=${encodeURIComponent(address.value)}`)
  const data = await response.json()
  console.log(data.results[0].geometry.location)
}


</script>

<style scoped>

</style>