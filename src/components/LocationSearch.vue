<script>
export default {
  data() {
    return {
      location: '',
      map: null,
      list: 0,
      apiKey: import.meta.env.VITE_APP_LOCATION_API,
      currentMap: `https://maps.geoapify.com/v1/staticmap?style=osm-carto&width=600&height=400&center=lonlat:-1.989935,52.518458&zoom=5.6&apiKey=${import.meta.env.VITE_APP_LOCATION_API}`,
      defaultMap: `https://maps.geoapify.com/v1/staticmap?style=osm-carto&width=600&height=400&center=lonlat:-1.989935,52.518458&zoom=5.6&apiKey=${import.meta.env.VITE_APP_LOCATION_API}`,
      searchedLocations: [],
    }
  },
  methods: {
    submit(event) {
      // event.preventDefault()
      console.log(this.location)
      fetch(`https://api.geoapify.com/v1/geocode/search?text=${this.location}&format=json&apiKey=${this.apiKey}`)
        .then(response => response.json())
        .then(result => {
          console.log(result.results[0])
          const point = result.results[0]
          console.log(point.lat, point.lon)
          this.list++
          this.currentMap = `https://maps.geoapify.com/v1/staticmap?style=osm-carto&width=600&height=400&center=lonlat:${point.lon},${point.lat}&zoom=5.6&marker=lonlat:${point.lon},${point.lat};color:%23ff0000;size:medium&apiKey=${import.meta.env.VITE_APP_LOCATION_API}`
          console.log(this.currentMap)
          this.searchedLocations.push(this.list + ") " + point.address_line1 + ", " + point.address_line2)
        })
    },
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
  </div>
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