<script>
export default {
  data() {
    return {
      name: 'Vue.js',
      latitude: '',
      longitude: '',
      city: '',
      province: '',
      country: '',
      apiKey: import.meta.env.VITE_APP_LOCATION_API,
    }
  },
  methods: {
    getLocation(event) {
      event.preventDefault()
      navigator.geolocation.getCurrentPosition((position) => {
        this.latitude = position.coords.latitude
        this.longitude = position.coords.longitude
        fetch(`https://api.geoapify.com/v1/geocode/reverse?lat=${position.coords.latitude}&lon=${position.coords.longitude}&format=json&apiKey=${this.apiKey}`)
          .then(response => response.json())
          .then(result => {
            console.log(result.results[0])
            const { city, state, country } = result.results[0]
            this.city = city
            this.province = state
            this.country = country
          })
      })
    }
  }
}

</script>

<template>
  <div class="task one">
    <h3>
      <button @click="getLocation"> Click me to get location </button>
    </h3>
    <div>
      Your location is: {{ city }}, {{ province }}
    </div>
  </div>
</template>