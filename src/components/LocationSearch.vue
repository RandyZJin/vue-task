<script>
export default {
  data() {
    return {
      location: '',
      map: null,
      currentPage: 1,
      recordsPerPage: 10,
      id_serial: 1,
      apiKey: import.meta.env.VITE_APP_LOCATION_API,
      currentMap: `https://maps.geoapify.com/v1/staticmap?style=osm-carto&width=600&height=400&center=lonlat:-1.989935,52.518458&zoom=5.6&apiKey=${import.meta.env.VITE_APP_LOCATION_API}`,
      searchedLocations: [],
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
      fetch(`https://api.geoapify.com/v1/geocode/search?text=${this.location}&format=json&apiKey=${this.apiKey}`)
        .then(response => response.json())
        .then(result => {
          const point = result.results[0]
          let newSearchEntry = { query: this.location, longitude: point.lon, latitude: point.lat, id: this.id_serial, timezone: point.timezone.offset_DST }
          this.id_serial ++
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
      if (this.searchedLocations.length === 1) {
        baseQuery += `&center=lonlat:${this.searchedLocations[0].longitude},${this.searchedLocations[0].latitude}&zoom=8&marker=lonlat:${this.searchedLocations[0].longitude},${this.searchedLocations[0].latitude};color:%23ff0000;size:medium`
      }
      if (this.searchedLocations.length > 1) {
        baseQuery += `&marker=lonlat:${this.searchedLocations[0].longitude},${this.searchedLocations[0].latitude};color:%23ff0000;size:medium`
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

    getGoogleMapsLink(term) {
      return `<a href="https://maps.google.com/?q=${term}" target="_blank"> See in Google Maps </a>`
    },
  },

}
</script>

<template>
  <div >
    <div id="location-module">
      <form @submit.prevent="submit">
        <input v-model="location" placeholder="put a location here" />
        <button type="submit">Search For Location</button>
      </form>
      <img :src="currentMap" alt="Map">
    </div>

    <div>
      <button @click="removeSelectedLocations">Remove Selected</button>
      <div id="table-container">
        <table id="data-table">
          <thead>
            <tr>
              <th></th>
              <th>Search Terms</th>
              <th>Latitude, Longitude</th>
              <th>Timezone</th>
              <th>Current Time</th>
              <th></th>
            </tr>
          </thead>
          <tbody id="table-body">
            <tr v-for="location in displayedData" :key="location.id">
              <td>
                <input type="checkbox" v-model="location.selected">
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
              <td v-html="getGoogleMapsLink(location.query)">
              </td>
              <button @click="removeLocation(location.id)">Remove</button>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
    <div id="pagination-container">
      <button id="prev-btn" @click="goToPreviousPage" :disabled="currentPage === 1">Previous</button>
      <span id="page-num">Page {{ currentPage }}</span>
      <button id="next-btn" @click="goToNextPage" :disabled="currentPage === totalPages">Next</button>
    </div>
  </div>
</template>

<style scoped>
#location-module {
  display: flex;
  flex-direction: column;
  justify-self: center;
}


#my-map {
  height: 400px;
}

#table-container {
  overflow-y: auto;
  border: black;
}

#table-container td,
th {
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