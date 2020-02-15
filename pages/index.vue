<template lang="pug">
  div
    div#map
</template>

<script>
// import L from 'leaflet'

export default {
  data() {
    return {}
  },
  mounted() {
    this.$axios.get('https://raw.githubusercontent.com/kiang/pharmacies/master/json/points.json').then(res => {
      console.log(res)
    })
  }
}
</script>
