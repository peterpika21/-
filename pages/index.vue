<template lang="pug">
  div.font-sans
    div.bg__Block
    h1.bg__text
      p.bg__text--en Mask Map
      p.bg__text--zh 
        span.tx-primary 口罩
        span 地圖

    // 主畫面
    section.wrap
      // 左側
      div.info
        // 上方資訊
        div.p-2.bg-primary
          div.mb-2.d-flex.justify-content-between
            p.font-l.tx-white 星期二
            div.align-right
              p.font-m.tx-white 可購買身分證末碼為
              p.font-n.weight-l.tx-info 2,4,6,8,0
          input.p-2.width-100.border-0.round-l(type="text",placeholder="請輸入藥局名稱")

        // 藥局列表
        ul.info__list
          li.p-2.bg-white.border(v-for="item in mapData",style="border-bottom: 1px solid #ddd;")
            p.mb-1.weight-l {{ item.properties.name }}
            p.mb-1.font-m.tx-gray-300 {{ item.properties.address }}
            p.mb-1.font-m.tx-gray-300 {{ item.properties.phone }}
            
            div.d-flex.tx-white
              div.mr-4.py-1.px-3.flex-1.bg-primary.round-l.border-0.font-m.weight-l.d-flex.justify-content-between
                span 成人口罩
                span {{ item.properties.mask_adult }}
              div.py-1.px-3.flex-1.bg-child.round-l.border-0.font-m.weight-l.d-flex.justify-content-between
                span 兒童口罩 
                span {{ item.properties.mask_child }}

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
