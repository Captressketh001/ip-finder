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
        <div class="address-info" v-if="!loading && responses">
          <div>
            <p>IP ADDRESS</p>
            <h2>{{responses.ip}}</h2>
          </div>
          <div>
            <p>LOCATION</p>
            <h2>{{responses.city}}, {{responses.region}}</h2>
          </div>
          <div>
            <p>TIMEZONE</p>
            <h2>UTC{{responses.timezone}}</h2>
          </div>
          <div>
            <p>ISP</p>
            <h2>{{responses.isp}}</h2>
          </div>
        </div>
        <div class="address-info" v-if="!loading && !responses" >
          <h2>Not found. Input correct IPv4 or IPv6 address.</h2>
        </div>
      </div>
      <div style="height:600px; z-index: -1;" id="map">
        <!-- <l-map :zoom="zoom" :center="center">
          <l-tile-layer :url="url" :attribution="attribution">
             <l-geo-json :geojson="geojson" :lat-lng="withPopup"/>
          </l-tile-layer>
         <l-marker :lat-lng="withPopup">
       
          </l-marker>
           <img src="/images/icon-location.svg" alt="" srcset="" :lat-lng="withPopup">
        </l-map> -->
      </div>
    </header>
    </div>
</template>

<script>
import "leaflet/dist/leaflet.css"
import leaflet from "leaflet";
// import { LMap, LTileLayer, LMarker} from "@vue-leaflet/vue-leaflet";
export default {
  name: 'App',
  // components: {
  //   LMap,
  //   LTileLayer,
  //   LMarker
  // }, 
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
        const res = await response.json();
        this.responses = {
          ip: res.ip,
          city: res.location.city,
          region: res.location.region,
          timezone: res.location.timezone,
          isp: res.isp,
          lat: res.location.lat,
          lng: res.location.lng
        }
        this.center[0] = this.responses.lat
        this.center[1] = this.responses.lng
        this.latitude = parseFloat(this.responses.lat)
        this.longitude = parseFloat(this.responses.lng)
        // this.withPopup = latLng(6.45407, 3.39467)
        // console.log(this.withPopup)
        let loadmap;
       loadmap = leaflet.map('map').setView([51.505, -0.09], 13);
      leaflet
        .tileLayer(this.url, {
          maxZoom: 19,
          attribution:
            this.attribution,
        })
        .addTo(loadmap);
        
        loadmap.setView([this.latitude, this.longitude], 13);
        this.loading = false;
        var marker = leaflet.icon({
            iconUrl: '/images/icon-location.svg',
            // shadowUrl: 'leaf-shadow.png',

            iconSize:     [30, 30], // size of the icon
            // shadowSize:   [50, 64], // size of the shadow
            iconAnchor:   [22, 94], // point of the icon which will correspond to marker's location
            // shadowAnchor: [4, 62],  // the same for the shadow
            popupAnchor:  [-3, -76] // point from which the popup should open relative to the iconAnchor
        });
        leaflet
          .marker([this.latitude, this.longitude], {icon: marker})
          .addTo(loadmap);
        this.address = ''
      } catch(e){
        // alert('error', e)
        // this.responses = [];
        this.address = ''
        this.loading = false;
      }

    }
  },
  beforeMount() {
     this.findIp();
    //  this.addmap()
  },

 
}
</script>

<style>

</style>
