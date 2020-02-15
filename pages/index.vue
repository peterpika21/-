<template lang="pug">
  div
    div.bg__Block
    h1.bg__text
      p.bg__text--en Mask Map
      p.bg__text--zh 
        span.tx-primary 口罩
        span 地圖

    // 主畫面
    section.wrap
      // 左側列表
      div
        //- ul
        //-   li(v-for="item in mapData")
        //-     p {{ item.properties.name }}

      // 地圖
      div#map
</template>

<script>
// import L from 'leaflet'

export default {
  data() {
    return {
      mapData: []
    }
  },
  mounted() {
    this.$axios.get('https://raw.githubusercontent.com/kiang/pharmacies/master/json/points.json').then(res => {
      if (res.status === 200) {
        this.mapData = res.data.features
        console.log(this.mapData)
      }
    })
  }
}
</script>
