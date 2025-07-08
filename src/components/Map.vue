<script setup>
import {MglMap, MglMarker, useMap} from "@indoorequal/vue-maplibre-gl";
import {ref, watchEffect} from "vue";
import {useToast} from "vue-toastification";
import axios from 'axios';

let newPos = [0, 0];
const coordinates = ref(newPos);
const mapRef = useMap();
const toast = useToast();

const updatePosition = async () => {

  try {
    const response = await axios.get(`/api`);
    const issPositon = response.data;
    const lat = issPositon.iss_position.latitude;
    const long = issPositon.iss_position.longitude;
    newPos = [Number(long), Number(lat)];
    console.log(newPos);


  } catch (error) {
    toast.warning("Oops... Something went wrong")
    console.error('Error fetching position', error);
  }

  watchEffect(async () => {
    if (!mapRef.isLoaded || !mapRef.map) return;

    const location = newPos;
    coordinates.value = location;
    mapRef.map.flyTo({ center: location });
  })
};
</script>

<template>
  <div class="max-w-7xl mx-auto p-10 pb-50 sm:px-6 lg:px-8 flex flex-col items-center h-[800px] bg-neutral-800">
    <div>
      <button @click="updatePosition()"
              class="bg-violet-500 hover:bg-violet-600
          text-white font-bold mb-5 py-2 px-4
          rounded-full w-full hover:cursor-pointer focus:outline-none focus:shadow-outline">
        Update Position
      </button>
    </div>
    <mgl-map :center="coordinates"
             map-style="https://tiles.openfreemap.org/styles/liberty"
             :zoom=3>
      <mgl-marker :coordinates="coordinates" :center="coordinates" color="#7f22fe" >
      </mgl-marker>
    </mgl-map>

  </div>

</template>



<style>
@import "maplibre-gl/dist/maplibre-gl.css";
</style>