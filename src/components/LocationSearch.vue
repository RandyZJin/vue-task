<script>
export default {
  data() {
    return {
      location: '',
      map: null,
      currentPage: 1,
      recordsPerPage: 10,
      apiKey: import.meta.env.VITE_APP_LOCATION_API,
      currentMap: `https://maps.geoapify.com/v1/staticmap?style=osm-carto&width=600&height=400&center=lonlat:-1.989935,52.518458&zoom=5.6&apiKey=${import.meta.env.VITE_APP_LOCATION_API}`,
      searchedLocations: [],
      // searchedLocations: [{ query: "o.co coliseum", longitude: -113.45838, latitude: 53.570676, id: 1, timezone: "-07:00" }, { query: "hawthorns", longitude: -9.16483646954769, latitude: 54.1274345, id: 2, timezone: "+01:00" }, { query: "o.co coliseum", longitude: -113.45838, latitude: 53.570676, id: 3, timezone: "-07:00" }, { query: "o.co coliseum", longitude: -113.45838, latitude: 53.570676, id: 4, timezone: "-07:00" }, { query: "o.co coliseum", longitude: -113.45838, latitude: 53.570676, id: 5, timezone: "-07:00" }, { query: "o.co coliseum", longitude: -113.45838, latitude: 53.570676, id: 6, timezone: "-07:00" }, { query: "o.co coliseum", longitude: -113.45838, latitude: 53.570676, id: 7, timezone: "-07:00" }, { query: "o.co coliseum", longitude: -113.45838, latitude: 53.570676, id: 8, timezone: "-07:00" }, { query: "o.co coliseum", longitude: -113.45838, latitude: 53.570676, id: 9, timezone: "-07:00" }, { query: "o.co coliseum", longitude: -113.45838, latitude: 53.570676, id: 10, timezone: "-07:00" }, { query: "o.co coliseum", longitude: -113.45838, latitude: 53.570676, id: 11, timezone: "-07:00" }, { query: "o.co coliseum", longitude: -113.45838, latitude: 53.570676, id: 12, timezone: "-07:00" }, { query: "o.co coliseum", longitude: -113.45838, latitude: 53.570676, id: 13, timezone: "-07:00" }, { query: "o.co coliseum", longitude: -113.45838, latitude: 53.570676, id: 14, timezone: "-07:00" }, { query: "o.co coliseum", longitude: -113.45838, latitude: 53.570676, id: 15, timezone: "-07:00" }],

    }
  },
  computed: {
    totalPages() {
      return Math.ceil(this.searchedLocations.length / this.recordsPerPage)
    },
    displayedData() {
      const startIndex = (this.currentPage - 1) * this.recordsPerPage
      const endIndex = startIndex + this.recordsPerPage
      return this.searchedLocations.slice(startIndex, endIndex)
    }
  },
  methods: {
    submit(event) {
      event.preventDefault()
      console.log(this.location)
      fetch(`https://api.geoapify.com/v1/geocode/search?text=${this.location}&format=json&apiKey=${this.apiKey}`)
        .then(response => response.json())
        .then(result => {
          console.log(result.results[0])
          const point = result.results[0]
          console.log(point.lat, point.lon)
          // this.currentMap = `https://maps.geoapify.com/v1/staticmap?style=osm-carto&width=600&height=400&center=lonlat:${point.lon},${point.lat}&zoom=5.6&marker=lonlat:${point.lon},${point.lat};color:%23ff0000;size:medium&apiKey=${import.meta.env.VITE_APP_LOCATION_API}`
          // console.log(this.currentMap)
          // this.searchedLocations[this.location] = point.address_line1 + ", " + point.address_line2
          // if (this.searchedLocations.length = 10) {
          //   this.searchedLocations.pop()
          // }
          let newSearchEntry = { query: this.location, longitude: point.lon, latitude: point.lat, id: this.searchedLocations.length + 1, timezone: point.timezone.offset_DST}
          this.searchedLocations.push(newSearchEntry)
          this.updateMap()
        })
    },
    removeLocation(id) {
      const index = this.searchedLocations.findIndex(location => location.id === id)
      if (index !== -1) {
        this.searchedLocations.splice(index, 1)
      }
      this.updateMap()
    },
    removeSelectedLocations() {
      this.searchedLocations = this.searchedLocations.filter(location => !location.selected)
      this.updateMap()
    },
    updateMap() {
      let baseQuery = `https://maps.geoapify.com/v1/staticmap?style=osm-carto&width=600&height=400`
      if (this.searchedLocations.length > 0) {
        baseQuery += `&marker=lonlat:${this.searchedLocations[0].longitude},${this.searchedLocations[0].latitude};color:%23ff0000;size:medium`
      }
      if (this.searchedLocations.length > 1) {
        for (let index = 1; index < this.searchedLocations.length; index++) {
          baseQuery += `|lonlat:${this.searchedLocations[index].longitude},${this.searchedLocations[index].latitude};color:%23ff0000;size:medium`
        }
      }
      baseQuery += `&apiKey=${import.meta.env.VITE_APP_LOCATION_API}`
      this.currentMap = baseQuery

    },

    goToPreviousPage() {
      if (this.currentPage > 1) {
        this.currentPage--
      }
    },
    goToNextPage() {
      if (this.currentPage < this.totalPages) {
        this.currentPage++
      }
    },

    getLocalTime(timezone) {
      const today = new Date()
      const localTime = today.getHours() + 4 + Number(timezone.split(":")[0]) + ":"
      // adding +4 as it is GMT -4 right now for EDT
      if (today.getMinutes() < 10) {
        return localTime + 0 + today.getMinutes()
      }
      return localTime + today.getMinutes()
    },


  },

}
</script>

<template>
  <div>

    <form @submit.prevent="submit">
      <input v-model="location" placeholder="put a location here" />
      <button type="submit">Search</button>
    </form>
    <img :src="currentMap" alt="Map">
  </div>
  <div>
    {{ searchedLocations }}
    {{ searchedLocations.length }}
  </div>

  <!-- <ul>
    <li v-for="location of searchedLocations">
      <input type="checkbox" id=${location.query} value=location.query v-model="checkedLocations">
      <label for=location.query>{{location.query}}</label>
    </li>
  </ul> -->
  <!-- <ul>
    <li v-for="location of searchedLocations">
      {{location.query}}
    </li>
  </ul> -->
  <div>
    <button @click="removeSelectedLocations">Remove Selected</button>
    <div id="table-container">
      <table id="data-table">
        <thead>
          <tr>
            <th></th>
            <th>id</th>
            <th>Search Terms</th>
            <th>Latitude, Longitude</th>
            <th>Timezone</th>
            <th>Current Time</th>
          </tr>
        </thead>
        <tbody id="table-body">
          <tr v-for="location in displayedData" :key="location.id">
            <td>
              <input type="checkbox" v-model="location.selected">
            </td>
            <td>
              {{ location.id }}
            </td>
            <td>
              {{ location.query }}
            </td>
            <td>
              {{ location.latitude }} , {{ location.longitude }}
            </td>
            <td>
              GMT {{ location.timezone }}
            </td>
            <td>
              {{ getLocalTime(location.timezone) }}
            </td>
            <button @click="removeLocation(location.id)">Remove</button>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
  <!-- {{ displayedData }} -->

  <div id="pagination-container">
    <button id="prev-btn" @click="goToPreviousPage" :disabled="currentPage === 1">Previous</button>
    <span id="page-num">Page {{ currentPage }}</span>
    <button id="next-btn" @click="goToNextPage" :disabled="currentPage === totalPages">Next</button>
  </div>
</template>

<style scoped>


#my-map {
  height: 400px;
}

#table-container {
  /* max-height: 300px; */
  overflow-y: auto;
  border: black;
}

#table-container td,
th {
  /* max-height: 300px; */
  overflow-y: auto;
  border: cornflowerblue;
  border: 1px solid black;
}


#pagination-container {
  text-align: center;
  margin-top: 10px;
}

#prev-btn,
#next-btn {
  margin: 0 5px;
}
</style>