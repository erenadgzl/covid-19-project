<template>

<div id="app">
  <v-app id="inspire">
    <v-card>
      <v-card-title>
        CORONAVIRUS CASES :<strong class="red--text">{{coronaVirusCases}}</strong>
        <v-divider
          class="mx-4"
          inset
          vertical
        ></v-divider>
        Deaths :<strong class="red--text">{{deaths}}</strong>
        <v-spacer></v-spacer>
      </v-card-title>
      <div id="map-wrap" style="height: 75vh">
            <no-ssr>
              <l-map :zoom="3"  :center="center">
                <l-tile-layer :url="url"></l-tile-layer>
                  <l-circle-marker v-for="item in countries" :key="item.id"
                    :lat-lng="[item.coordinates.latitude,item.coordinates.longitude]"
                    :radius="10"
                    :color="getColor(item.latest.confirmed)"
                    :fillColor="getColor(item.latest.confirmed)"
                    :weight="1"
                    :opacity="0"
                    :fillOpacity="0.7"
                  >
                  <l-popup><p><strong>{{item.country}} {{item.province}}</strong></p><p>Population:{{item.country_population}}</p>  <strong v-bind:style="{ color: getColor(item.latest.confirmed)}">CASES:{{item.latest.confirmed}}</strong></l-popup>
                  </l-circle-marker>
              </l-map>
            </no-ssr>
      </div>  
    </v-card>
  </v-app>
</div>
</template>
<script>
export default {
  data () {
    return {
      countries: [],
      coronaVirusCases: 0,
      deaths: 0,
      url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
      zoom: 8,
      center: [47.313220, -1.319482],
      
      radius: 70000,
      color: 'red'
    }
  },
  methods:{
    async asyncData() {
      const data = await this.$axios.$get('https://coronavirus-tracker-api.herokuapp.com/v2/locations')
      this.countries=data.locations
      this.deaths=data.latest.deaths
      this.coronaVirusCases=data.latest.confirmed
    },
    getColor (count) {
      if (count > 5000) return 'red'
      else if (count > 2500) return '#ff5b00'
      else if (count > 1000) return '#ff8f00'
      else if (count > 500) return '#ffc302'
      else if (count > 50) return '#FFEA00'
      else return 'green'
    }
  },
  mounted(){
    this.asyncData()
    //var mymap = L.map('mapid').setView([51.505, -0.09], 13);
  }
}
</script>