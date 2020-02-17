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
            p.font-l.tx-white 星期{{day('week')}}
            div.align-right
              p.font-m.tx-white 可購買身分證末碼為
              p.font-n.weight-l.tx-info {{day('num')}}

          div.d-flex.justify-content-between
            select.border-0.weight-l(v-model="select.city",@change="clear()")
              option(value="") 請選擇城市
              option(v-for="item in city",:value="item.CityName") {{item.CityName}}
            select.border-0.weight-l(v-model="select.area",v-if="select.city.length",@change="getMarker()")
              option(value="") 請選擇城市
              option(v-for="item in city.find(e => e.CityName === select.city).AreaList",:value="item.AreaName") {{item.AreaName}}

        // 藥局列表
        ul.info__list
          li.p-2.bg-white.border.cursor-pointer(v-for="item in markers",@click="clickMarker(item)",style="border-bottom: 1px solid #ddd;")
            p.mb-1.weight-l {{ item.properties.name }}
            p.mb-1.font-m.tx-gray-300 {{ item.properties.address }}
            p.mb-1.font-m.tx-gray-300 {{ item.properties.phone }}
            
            div.d-flex.tx-white
              div.mr-4.py-1.px-3.flex-1.bg-primary.round-l.border-0.font-m.weight-l.d-flex.justify-content-between(:class="{'bg-gray-200':(item.properties.mask_adult === 0)}")
                span 成人
                span {{ item.properties.mask_adult }}
              div.py-1.px-3.flex-1.bg-child.round-l.border-0.font-m.weight-l.d-flex.justify-content-between(:class="{'bg-gray-200':(item.properties.mask_child === 0)}")
                span 兒童 
                span {{ item.properties.mask_child }}
          
      // 地圖
      gmap-map(ref="mapRef",:center="center",:map-type-id="'terrain'",:zoom="15",:options="options")
        gmap-marker(v-for="(item, index) in markers",:key="index",:clickable="true",:position="item.position",@click="clickMarker(item)")
        gmap-info-window(@closeclick="isOpenWindow=false" ,:opened="isOpenWindow" ,:position="center",:options="{pixelOffset: {width: 0,height: -35 }}")
          div(v-html="windowTheme")
</template>

<script>
import city from '~/assets/js/cityName.json'

export default {
  data() {
    return {
      city,
      select: {
        city: '',
        area: ''
      },
      markers: [],
      isOpenWindow: false,
      center: { lat: 24.850235, lng: 121.095865 },
      windowTheme: '',
      options: {
        zoomControl: false, // 設定顯示縮放大小的控制鈕
        mapTypeControl: false, // 設定是否讓使用者可以切換地圖樣式
        streetViewControl: false, // 設定是否呈現右下角街景小人
        fullscreenControl: false // 設定是否讓使用者可以點擊開啟全螢幕地圖功能
      }
    }
  },
  mounted() {
    let vm = this
    // 開啟定位
    navigator.geolocation.getCurrentPosition(function(pos) {
      vm.center = { lat: pos.coords.latitude, lng: pos.coords.longitude }
    })
  },
  methods: {
    getMarker() {
      this.isOpenWindow = false

      this.$axios.get('https://raw.githubusercontent.com/kiang/pharmacies/master/json/points.json').then(res => {
        if (res.status === 200) {
          let temp = res.data.features

          let area = temp.filter(
            e => e.properties.county === this.select.city && e.properties.town === this.select.area
          )

          this.markers = area.map(e => ({
            position: { lat: e.geometry.coordinates[1], lng: e.geometry.coordinates[0] },
            properties: e.properties
          }))
        }
      })
    },
    clickMarker(item) {
      this.center = item.position
      this.isOpenWindow = true

      this.windowTheme = `
      <div style="width: 260px;">
        <p class="mb-1 weight-l">${item.properties.name}</p>
        <p class="mb-1 font-m tx-gray-300">${item.properties.address}</p>
        <p class="mb-1 font-m tx-gray-300">${item.properties.phone}</p>
        <div class="d-flex tx-white">
          <div class="mr-4 py-1 px-3 flex-1 bg-primary round-l border-0 font-m weight-l d-flex justify-content-between 
          ${item.properties.mask_adult ? '' : 'bg-gray-200'}">
            <span>成人</span>
            <span>${item.properties.mask_adult}</span>
          </div>

          <div class="mr-4 py-1 px-3 flex-1 bg-child round-l border-0 font-m weight-l d-flex justify-content-between
          ${item.properties.mask_child ? '' : 'bg-gray-200'}">
            <span>兒童</span>
            <span>${item.properties.mask_child}</span>
          </div>
        </div>
      </div>
      `
    },
    clear() {
      this.select.area = ''
      this.isOpenWindow = false
      this.markers = []
    },
    day(type) {
      let time = new Date().getDay()
      let day = ''
      let num = ''

      switch (time) {
        case 1:
          day = '一'
          num = '1,3,5,7,9'
          break
        case 2:
          day = '二'
          num = '0,2,4,6,8'
          break
        case 3:
          day = '三'
          num = '1,3,5,7,9'
          break
        case 4:
          day = '四'
          num = '0,2,4,6,8'
          break
        case 5:
          day = '五'
          num = '1,3,5,7,9'
          break
        case 6:
          day = '六'
          num = '0,2,4,6,8'
          break
        default:
          day = '日'
          num = '全部'
          break
      }
      if (type === 'week') {
        return day
      } else {
        return num
      }
    }
  }
}
</script>
