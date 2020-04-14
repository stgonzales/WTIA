<template>
  <div class="hello">
    <div v-if="loading">
      <p>Loading ...</p>
    </div>

    <div v-if="err">
      {{err}}
    </div>

    <div v-if="center" id="mapid">
      <l-map style="height: 400px" :zoom="zoom" :center="center">
      <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
      <l-marker :lat-lng="center" ></l-marker>
      </l-map>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import { latLng, Icon } from "leaflet";

// this part resolve an issue where the markers would not appear
delete Icon.Default.prototype._getIconUrl;

Icon.Default.mergeOptions({
    iconRetinaUrl: require('leaflet/dist/images/marker-icon-2x.png'),
    iconUrl: require('leaflet/dist/images/marker-icon.png'),
    shadowUrl: require('leaflet/dist/images/marker-shadow.png')
});

export default {
  name: 'Iss',
  data(){
    return{
      loading: true,
      err: null,
      post: null,
      zoom: 4,
      center: null,
      url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
      attribution: '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      currentZoom: 11.5,
      showParagraph: false,
      // icon:L.icon({
      //   iconUrl: require('../assets/logo.png'),
      //   iconSize: [32, 37],
      //   iconAnchor: [16, 37]
      // }),
      mapOptions: {
        zoomSnap: 0.5
      },
      showMap: true
    }
  },
  created(){
    this.fetchData()
  },
  beforeDestroy(){

  },
  methods:{
    fetchData(){
      setInterval(() => {this.err = this.post = null
        axios.get('https://api.wheretheiss.at/v1/satellites/25544')
          .then(res => {
            this.loading = false
            this.center = latLng(res.data.latitude, res.data.longitude)
          })
          .catch(err => this.err = err)
      },3000)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
#mapid { 
  width: 850px;
  margin: auto;
}
</style>
