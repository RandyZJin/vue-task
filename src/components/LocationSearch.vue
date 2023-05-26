<script>
export default {
  data() {
    return {
      message: '',
      location: '',
      apiKey: import.meta.env.VITE_APP_LOCATION_API,
    }
  },
  methods: {
    submit(event) {
      // event.preventDefault()
      console.log(this.location)
      fetch(`https://api.geoapify.com/v1/geocode/search?text=${this.location}&format=json&apiKey=${this.apiKey}`)
        .then(response => response.json())
        .then(result => console.log(result))
    }
  }
}
</script>

<template>
  <!-- <p>Location is: {{ location }}</p> -->
  <form @submit.prevent="submit">
    <input v-model="location" placeholder="put a location here" />
    <button type="submit">Search</button>
  </form>
</template>