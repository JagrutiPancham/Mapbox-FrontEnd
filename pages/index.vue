
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
      zoom: 10,
      maxZoom: 22,
      crossSourceCollisions: false,
      failIfMajorPerformanceCaveat: false,
      preserveDrawingBuffer: true,
      hash: false,
      minPitch: 0,
      attributionControl: false,
      maxPitch: 60,
      optimizeForTerrain:true,
      scrollZoom:false,
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
  map.addControl(new MapboxglGeocoder({
      accessToken: mapboxgl.accessToken,
      mapboxgl: mapboxgl,
    })
)
  map.addControl(
    new mapboxgl.NavigationControl()
  );

map.addSource('Nagpur', {
'type': 'geojson',
'data': {
'type': 'Feature',
'geometry': {
'type': 'Polygon',
//<----boundary co-ordinate----->
'coordinates': [
  [
            [
              79.05624389648438,
              21.180570251575713
            ],
            [
              79.05075073242186,
              21.17608836283457
            ],
            [
              79.03633117675781,
              21.165203210480364
            ],
            [
              79.0411376953125,
              21.14599216495789
            ],
            [
              79.04731750488281,
              21.13702615752316
            ],
            [
              79.09126281738281,
              21.128059607618706
            ],
            [
              79.11666870117188,
              21.135745255030603
            ],
            [
              79.11941528320312,
              21.160720856165288
            ],
            [
              79.1070556640625,
              21.17864945874782
            ],
            [
              79.09538269042969,
              21.188893398817655
            ],
            [
              79.07341003417969,
              21.195295500861654
            ],
            [
              79.05624389648438,
              21.180570251575713
            ]
          ]
        ]
    }
  }
});
 
  map.addLayer({
  'id': 'Nagpur',
  'type': 'fill',
  'source': 'Nagpur', 
  'layout': {},
  'paint': {
  'fill-color': '#f02', 
  //rediish
  'fill-opacity': 0.5
  }
  });

  map.addLayer({
  'id': 'outline',
  'type': 'line',
  'source': 'Nagpur',
  'layout': {},
  'paint': {
  'line-color': '#000',
  //dark blackish
  'line-width': 3
  }
  });
  
}  
  
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

//<<<----------Upload file-------->>>
  //const map = []; 
  //  async function OnClick(event){
  //   console.log("file is uploaded");
  //   await $fetch("http://localhost:3001/map/upload",{
  //     method: "POST",
  //     body: JSON.stringify(data.Geolocation),      
  //   });
  // }


  // const layers = map.getStyle().layers;

// let firstSymbolId;
// for (const layer of layers) {
// if (layer.type === 'symbol') {
// firstSymbolId = layer.id;
// break;
// }
// }

// map.addSource('urban-areas', {
// 'type': 'geojson',
// 'data': 'https://docs.mapbox.com/mapbox-gl-js/assets/ne_50m_urban_areas.geojson'
// });
// map.addLayer(
// {
// 'id': 'urban-areas-fill',
// 'type': 'fill',
// 'source': 'urban-areas',
// 'layout': {},
// 'paint': {
// 'fill-color': '#f02',
// 'fill-opacity': 0.1
// }

// },
// firstSymbolId
// );