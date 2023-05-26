<script>
export default {
  data() {
    return {
      name: 'Vue.js',
      latitude: '',
      longitude: '',
    }
  },
  methods: {
    getLocation(event) {
      event.preventDefault()
      navigator.geolocation.getCurrentPosition((position)=> {
      console.log(position.coords.latitude, position.coords.longitude)
      this.latitude = position.coords.latitude
      this.longitude = position.coords.longitude
      })
    }
  }
}

</script>

<template>
  <div class="greetings">
    <h3>
      <button @click="getLocation"> Click me to get location </button>
    </h3>
    <div>
      Your location is: {{latitude}}, {{longitude}}
    </div>
  </div>
</template>