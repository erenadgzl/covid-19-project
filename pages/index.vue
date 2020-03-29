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
        <v-text-field
          v-model="search"
          append-icon="mdi-magnify"
          label="Search"
          single-line
          hide-details
        ></v-text-field>
      
        
        
      </v-card-title>
      <v-data-table
        :headers="headers"
        :items="countries"
        :search="search"
      >
        <template v-slot:item.latest.confirmed="{ item }">
          <v-chip :color="getColor(item.latest.confirmed)" dark>
            <strong class="white--text">{{ item.latest.confirmed }}</strong>
          </v-chip>
        </template>
        <template v-slot:item.last_updated="{ item }">
            {{ formatDate(item.last_updated) }}
        </template>
      </v-data-table>
    </v-card>
  </v-app>
</div>
</template>

<script>

export default {
  data () {
    return {
      search: '',
      headers: [
        {
          text: 'Country Code',
          align: 'start',
          sortable: false,
          value: 'country_code',
        },
        { text: 'Country', value: 'country' },
        { text: 'Country Population', value: 'country_population' },
        { text: 'Province', value: 'province' },
        { text: 'Confirmed', value: 'latest.confirmed' },
        { text: 'Deaths', value: 'latest.deaths' },
        { text: 'Recovered', value: 'latest.recovered' },
        { text: 'Last Updated', value: 'last_updated' },
      ],
      countries: [],
      coronaVirusCases: 0,
      deaths: 0,
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
    },
    formatDate(date) {
      var d = new Date(date),
          month = '' + (d.getMonth() + 1),
          day = '' + d.getDate(),
          year = d.getFullYear();
          
          var hour = '' + d.getHours();
          var minutes = '' + d.getMinutes();
          
      if (month.length < 2) 
          month = '0' + month;
      if (day.length < 2) 
          day = '0' + day;
      if (minutes.toString().length == 1) {
        minutes = "0" + minutes;
      }
      if (hour.toString().length == 1) {
        hour = "0" + hour;
      }    

      return [year, month, day ].join('-')+ ' ' + [hour, minutes].join(':');
    }
  },
  mounted(){
    this.asyncData()
  }
}
</script>