<template>
    <div>
      <header>
      <div class="address-container">
        <div class="title">
          <h1>IP Address Tracker</h1>
        </div>
         <div class="address-search">
          <input type="text" placeholder="Search for any IP address or domain" v-model="address">
          <button @click="findIp"><img src="/images/icon-arrow.svg" alt="" srcset=""></button>
        </div>
        <div class="address-info" v-if="loading">
          <h2>Loading...</h2>
        </div>
        <div class="address-info" v-else-if="!loading && responses.ip">
          <div>
            <p>IP ADDRESS</p>
            <h2>{{responses ? responses.ip: 'N/A'}}</h2>
            <b>{{responses.code}}</b>
          </div>
          <div>
            <p>LOCATION</p>
            <h2>{{responses ? responses.location.city: 'N/A'}}, {{responses ? responses.location.region: 'N/A'}}</h2>
          </div>
          <div>
            <p>TIMEZONE</p>
            <h2>UTC{{responses ? responses.location.timezone : 'N/A'}}</h2>
          </div>
          <div>
            <p>ISP</p>
            <h2>{{responses ? responses.isp : 'N/A'}}</h2>
          </div>
        </div>
        <div class="address-info" v-else>
          <h2>Not found. Input correct IPv4 or IPv6 address.</h2>
        </div>
      </div>
      <div>
        <l-map style="height:600px; z-index: -1;" :zoom="zoom" :center="center">
          <l-tile-layer :url="url" :attribution="attribution">
             <!-- <l-geo-json :geojson="geojson" :lat-lng="withPopup"/> -->
          </l-tile-layer>
         <l-marker :lat-lng="withPopup">
       
          </l-marker>
           <!-- <img src="/images/icon-location.svg" alt="" srcset="" :lat-lng="withPopup"> -->
        </l-map>
      </div>
    </header>
    </div>
</template>

<script>
import "leaflet/dist/leaflet.css"
import { latLng } from "leaflet";
import { LMap, LTileLayer, LMarker} from "@vue-leaflet/vue-leaflet";
export default {
  name: 'App',
  components: {
    LMap,
    LTileLayer,
    LMarker
  }, 
  data() {
    return {
      latitude:'',
      longitude:'',
      address:'',
      loading: false,
      message:'',
      geojson: {
        "type": "FeatureCollection",
        "features": [{
          "type": "Feature",
          "geometry": {
            "type": "LineString",
            "coordinates": [
              [6.45407, 3.394677],
              [174.346433, -35.739337]
            ]
          },
          "properties": {
            "in_port": true,
            "id": 12824
          }
          }]
        },
        zoom: 13,
      center: [],
      url: 'http://{s}.tile.osm.org/{z}/{x}/{y}.png',
      attribution: '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      geojsonOptions: {
        radius: 8,
        fillColor: "#ff7800",
        color: "#000",
        weight: 1,
        opacity: 1,
        fillOpacity: 0.8
      },
      responses:[],
      withPopup: null,
    };
  },
  methods:{
    async findIp(){
      try{
        this.loading = true;
        const response = await fetch('https://geo.ipify.org/api/v2/country,city?apiKey=at_PDpgwRUasMPnp5TsmfLjPLx6Nwta0&ipAddress='+ this.address);
        this.responses = await response.json();
        this.center[0] = this.responses.location.lat
        this.center[1] = this.responses.location.lng
        this.latitude = parseFloat(this.responses.location.lat)
        this.longitude = parseFloat(this.responses.location.lng)
        this.withPopup = latLng(6.45407, 3.39467)
        console.log(this.withPopup)
        this.loading = false;
      } catch(e){
        this.responses = [];
        this.address = ''
        this.loading = false;
      }

    }
  },
  beforeMount() {
     this.findIp();
  },

 
}
</script>

<style>

</style>
