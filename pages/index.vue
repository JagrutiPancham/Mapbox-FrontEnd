
<template>
    <main class="w-screen h-screen">
        <label>Upload a File  </label>
        <input  id="geolocation" type="file" />
        <br/>

        <!-- <input placeholder="Enter destination" type="text"/> -->
        <!-- <button  @click="OnClick">Upload</button>
        <button @click="getMarkerApi">Get</button>
         -->
        <v-map class="w-full h-full" :options="state.map" @Loaded="OnMapLoaded" />
    </main>
</template>
<script setup lang="ts" >
  import MapboxglGeocoder from "@mapbox/mapbox-gl-geocoder";
  import "@mapbox/mapbox-gl-geocoder/dist/mapbox-gl-geocoder.css"

import mapboxgl from 'mapbox-gl';    
import VMap from 'v-mapbox';

mapboxgl.accessToken="pk.eyJ1IjoiamFncnV0aXBhbmNoYW0iLCJhIjoiY2w2Zzg1enoxMDFsMDNkdHVzNTZnY3V4YiJ9.cqnFY35YmBR6HfS8A1w7xA"
  const state = reactive({
    data:[],
    map: {
      container: "map",
      // accessToken:
      //   "pk.eyJ1IjoiamFncnV0aXBhbmNoYW0iLCJhIjoiY2w2Zzg1enoxMDFsMDNkdHVzNTZnY3V4YiJ9.cqnFY35YmBR6HfS8A1w7xA",
      style: "mapbox://styles/mapbox/streets-v11?optimize=true",
      center: [79.088860,21.146633] as number[],
      zoom: 8,
      maxZoom: 22,
      crossSourceCollisions: false,
      failIfMajorPerformanceCaveat: false,
      preserveDrawingBuffer: true,
      hash: false,
      minPitch: 0,
      attributionControl: false,
      maxPitch: 60,
      optimizeForTerrain:true,
  
   } as mapboxgl.MapboxOptions,
   mapdata: {} as mapboxgl.Map,
  });

  
 // GET API
async function OnMapLoaded(map){
  console.log("get api is working");
  state.data = await $fetch("http://localhost:3001/map/details"); 
  console.log("map is loaded!");
  for(const feature of state.data ){
    console.log("Points: "+feature);
    const marker = new mapboxgl.Marker({
          color: "red",
          }).setLngLat(feature.Point.coordinates).addTo(map);
  }  
  
  map.addControl(
    new MapboxglGeocoder({
      accessToken: mapboxgl.accessToken,
      mapboxgl: mapboxgl,
    })
  );


  const layers = map.getStyle().layers;

let firstSymbolId;
for (const layer of layers) {
if (layer.type === 'symbol') {
firstSymbolId = layer.id;
break;
}
}

map.addSource('urban-areas', {
'type': 'geojson',
'data': 'https://docs.mapbox.com/mapbox-gl-js/assets/ne_50m_urban_areas.geojson'
});
map.addLayer(
{
'id': 'urban-areas-fill',
'type': 'fill',
'source': 'urban-areas',
'layout': {},
'paint': {
'fill-color': '#f02',
'fill-opacity': 0.1
}

},
firstSymbolId
);

 
}  
//<<<----------Upload file-------->>>
  //const map = []; 
  //  async function OnClick(event){
  //   console.log("file is uploaded");
  //   await $fetch("http://localhost:3001/map/upload",{
  //     method: "POST",
  //     body: JSON.stringify(data.Geolocation),      
  //   });
  // }


  
  
  </script>
  <style>
  .w-screen {
    width: 100vw;
  }
  .h-screen {
    height: 100vh;
  }
  .h-full {
    height: 100%;
  }
  .w-full {
    width: 100%;
  }
  </style>