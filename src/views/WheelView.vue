<template>
  <div>
    <h1>Welcome to the Wheel View!</h1>
    <p>This is a simple Vue component.</p>
    <button @click="getLocation">Get Location!</button>
    <button @click="geocode">Geocode!</button>
    <input type="text" v-model="address" placeholder="Enter address">
    <p>Address: {{ address }}</p>
    <p>Location: {{ location }}</p>
    <button @click="getPlaces">Get Places!</button>
    <p>{{ places }}</p>
    <Roulette ref="wheel" :items="items" horizontal-content @click="launchWheel" />
  </div>
</template>

<script setup lang="ts">

import { ref, computed } from 'vue'
import { Roulette } from 'vue3-roulette'

const address = ref('')

// Define a type for lat and long location
type Location = {
  lat: number;
  lng: number;
}

const location = ref<Location>({ lat: 0, lng: 0 })

const places = ref([])

// Wheel items shoudl be a computed property based on places, but adding props for name, htmlContent, textColor, and background
const items = computed(() => places.value.map((place: string, index: number) => ({
  id: index,
  name: place,
  htmlContent: place,
  textColor: "black",
  background: ""
})))





function getLocation() {
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(showPosition, showError);
      } else {
        console.log("Geolocation is not supported by this browser.");
      }
    }


    function showPosition(position) {
      console.log(position)
      location.value = {
        lat: position.coords.latitude,
        lng: position.coords.longitude
      }
    }

    function showError(error) {
      console.log(error)
    }




async function geocode() {
  console.log('Geocoding...')
  const response = await fetch(`https://wheel-of-proxy.niftyoctopus.workers.dev/geolocation?address=${encodeURIComponent(address.value)}`)
  const data = await response.json()
  const result = data.results[0].geometry.location
  console.log(result)
  location.value = result
}


// Make a call to get places
// This will again to to the proxy server but with the /places endpoint
// The request format should be for the google nearby places API (new)

async function getPlaces() {
  console.log('Getting places...')
  // POST request to the proxy server

  const body = {
    "includedTypes": ["pizza_restaurant"],
    "maxResultCount": 5,
    "locationRestriction": {
      "circle": {
        "center": {
          "latitude": location.value.lat,
          "longitude": location.value.lng
        },
        "radius": 2000.0
      }
    }
  }


  const response = await fetch('https://wheel-of-proxy.niftyoctopus.workers.dev/places', {
    method: 'POST',
    headers: {
      'X-Goog-FieldMask': 'places.displayName',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify(body)
  })
  const data = await response.json()
  places.value = data.places.map((place: any) => place.displayName?.text);
}


const wheel = ref(null);

/*
const items = [
  { id: 1, name: "Banana", htmlContent: "Banana", textColor: "black", background: "" },
  { id: 2, name: "Apple", htmlContent: "Apple", textColor: "black", background: "" },
  { id: 3, name: "Orange", htmlContent: "Orange", textColor: "black", background: "" },
  { id: 4, name: "Cherry", htmlContent: "Cherry", textColor: "black", background: "" },
];*/

function launchWheel (){
  wheel.value.launchWheel();
}

</script>

<style scoped>

</style>