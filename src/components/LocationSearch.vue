<script>
export default {
  data() {
    return {
      location: '',
      map: null,
      // list: 0,
      apiKey: import.meta.env.VITE_APP_LOCATION_API,
      currentMap: `https://maps.geoapify.com/v1/staticmap?style=osm-carto&width=600&height=400&center=lonlat:-1.989935,52.518458&zoom=5.6&apiKey=${import.meta.env.VITE_APP_LOCATION_API}`,
      searchedLocations: [],
      // searchedLocations: [{ query: "o.co coliseum", longitude: -113.45838, latitude: 53.570676 }, { query: "hawthorns", longitude: -9.16483646954769, latitude: 54.1274345 }],
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
          // this.list++
          // this.currentMap = `https://maps.geoapify.com/v1/staticmap?style=osm-carto&width=600&height=400&center=lonlat:${point.lon},${point.lat}&zoom=5.6&marker=lonlat:${point.lon},${point.lat};color:%23ff0000;size:medium&apiKey=${import.meta.env.VITE_APP_LOCATION_API}`
          // console.log(this.currentMap)
          // this.searchedLocations[this.location] = point.address_line1 + ", " + point.address_line2
          // if (this.searchedLocations.length = 10) {
          //   this.searchedLocations.pop()
          // }
          let newSearchEntry = { query: this.location, longitude: point.lon, latitude: point.lat, id: this.searchedLocations.length + 1 }
          this.searchedLocations.push(newSearchEntry)
          console.log("line 32")
          console.log(newSearchEntry)
          console.log(this.searchedLocations)
          this.updateMap()
        })
    },
    removeLocation(id) {
      const index = this.searchedLocations.findIndex(location => location.id === id);
      if (index !== -1) {
        this.searchedLocations.splice(index, 1);
      }
      this.updateMap()
    },
    removeSelectedLocations() {
      this.searchedLocations = this.searchedLocations.filter(location => !location.selected);
      this.updateMap()
    },
    updateMap() {
      //this.currentMap = `https://maps.geoapify.com/v1/staticmap?style=osm-carto&width=600&height=400&center=lonlat:${point.lon},${point.lat}&zoom=5.6&marker=lonlat:${point.lon},${point.lat};color:%23ff0000;size:medium&apiKey=${import.meta.env.VITE_APP_LOCATION_API}`
      let baseQuery = `https://maps.geoapify.com/v1/staticmap?style=osm-carto&width=600&height=400&center=lonlat:`
      if (this.searchedLocations.length > 0) {
        baseQuery += `${this.searchedLocations[0].longitude},${this.searchedLocations[0].latitude}&zoom=5.6&marker=lonlat:${this.searchedLocations[0].longitude},${this.searchedLocations[0].latitude};color:%23ff0000;size:medium`
      }
      if (this.searchedLocations.length > 1) {
        for (let index = 1; index < this.searchedLocations.length; index++) {
          baseQuery += `|lonlat:${this.searchedLocations[index].longitude},${this.searchedLocations[index].latitude};color:%23ff0000;size:medium`
        }
      }
      baseQuery += `&apiKey=${import.meta.env.VITE_APP_LOCATION_API}`
      this.currentMap = baseQuery

    }

  },

}
</script>

<template>
  <!-- <p>Location is: {{ location }}</p> -->
  <div>

    <form @submit.prevent="submit">
      <input v-model="location" placeholder="put a location here" />
      <button type="submit">Search</button>
    </form>
    <img :src="currentMap" alt="Dynamic Image">
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
  <ol>
    <table v-for="location of searchedLocations" :key="location.id">
      <tr>
        <ul>
          <label>
            <input type="checkbox" v-model="location.selected">
            <td>
              {{ location.id }}
            </td>
            <td>
              {{ location.query }}
            </td>
            <td>
              {{ location.latitude }} , {{ location.longitude }}
            </td>
          </label>
          <button @click="removeLocation(location.id)">Remove</button>
        </ul>
      </tr>
    </table>
  </ol>
  <button @click="removeSelectedLocations">Remove Selected</button>
</template>

<style scoped>
/* @import '~maplibre-gl/dist/maplibre-gl.css'; */

.map-wrap {
  position: relative;
  width: 100%;
  height: calc(100vh - 77px);
  /* calculate height of the screen minus the heading */
}

.map {
  position: absolute;
  width: 100%;
  height: 100%;
}

.watermark {
  position: absolute;
  left: 10px;
  bottom: 10px;
  z-index: 999;
}

#my-map {
  height: 400px;
}
</style>