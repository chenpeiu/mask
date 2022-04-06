<template>
  <div class="mask flex">
    <button @click="log()">123</button>
    <div class="sidebar w-1/5 h-screen bg-slate-300 p-2 shadow-2xl">
      <div class="selectors w-full flex overflow-hidden mb-2 ">
        <select name="city" v-model="city" class="flex-1 text-center border-solid border-2 border-slate-600 rounded-md mr-2 py-1"> 
          <option value="" disabled >請選擇城市</option>
          <option :value="c.CityName" :key="c.CityName" v-for=" c in cityapi"> {{c.CityName}} </option>
        </select>
        <select name="area"  v-model="area" class="flex-1 text-center border-solid border-2 border-slate-600 rounded-md"> 
          <option value="" disabled >請選擇區域</option>
          <!-- <option :value="a.AreaName" :key="a.AreaName" v-for=" a in areafind"> {{a.AreaName}}</option> -->
          <option :value="a.AreaName" :key="a.AreaName" v-for=" a in cityapi.find( item => item.CityName == city).AreaList"> {{a.AreaName}}</option>
        </select>
      </div>
      <div class="wrap flex flex-col overflow-y-auto">
        <div class="item mb-2 p-2 bg-slate-500 rounded-md text-slate-200" :key="pharmacy.id" v-for="pharmacy in showitems">
          <div class="name text-xl underline decoration-slate-700">{{pharmacy.properties.name}}</div>
          <div class="txt ">
            <div class="phone text-xs"><fa :icon='["fas" , "phone"]' class="mr-1.5 text-[10px] text-slate-700"/>{{pharmacy.properties.phone}}</div>
            <div class="addr text-xs flex items-center">
              <fa :icon='["fas" , "house-medical"]' class="mr-1.5 text-[10px] text-slate-700"/>
              <span>{{pharmacy.properties.address}}</span>
            </div>
          </div>
          <div class="maskinform flex justify-cente mt-1">
            <div class="button mr-2">
              <span class="border-b-2 border-slate-700 text-xs py-0.5">成人口罩</span>
              <span class="text-sm py-0.5">{{pharmacy.properties.mask_adult}}</span>
            </div>
            <div class="button">
               <span class="border-b-2 border-slate-700 text-xs py-0.5">兒童口罩</span>
              <span class="text-sm py-0.5">{{pharmacy.properties.mask_child}}</span>
            </div>
          </div>
          <div class="detailicon py-1 mt-1 text-xs text-center rounded-sm bg-slate-300 text-slate-700 cursor-zoom-in no-underline hover:underline select-none" @click="pharmacy.properties.opendetail=!pharmacy.properties.opendetail" :class="{'opendetail_box': pharmacy.properties.opendetail}">營業時間</div>
          <div class="detail text-xs p-1 text-center bg-slate-300 text-slate-700 rounded-b-sm" v-show="pharmacy.properties.opendetail">{{pharmacy.properties.available}}</div>

        </div>
      </div>
    </div>
    <div id="map-wrap" style="height: 100vh" class="bg-rose-100 w-4/5">
      <client-only>
        <l-map :zoom="15" :center="center">
          <l-tile-layer url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png"></l-tile-layer>
          <l-marker :lat-lng="[dot.geometry.coordinates[1],dot.geometry.coordinates[0]]" :key="dot.properties.id" v-for="dot in showitems">
            <l-popup>
              <div class="name text-lg font-extrabold text-slate-700">{{dot.properties.name}}</div>
              <div class="tex text-[1px]">
                <span class="text-[1px]">成人：{{dot.properties.mask_adult}}</span>
                <span>兒童：{{dot.properties.mask_child}}</span>
                <div class="addr">地址：{{dot.properties.address}}</div>
                <div class="phone">電話：{{dot.properties.phone}}</div>
                <div>最後更新時間</div>
                <div class="update">{{dot.properties.updated}}</div>
              </div>
            </l-popup>
          </l-marker>
        </l-map>
      </client-only>
    </div>
  </div>
</template>
<style lang="sass">
  .mask
    .wrap
      height: 95%
      .item
        .button
          width: 50%
          text-align: center
          display: flex
          flex-direction: column
          border: solid 2px rgb(51 65 85)
          border-radius: 5px
        .opendetail_box
          border-radius: 0px
          border-top-left-radius: 0.125rem
          border-top-right-radius: 0.125rem
      
</style>
<script>
import axios from 'axios'
export default {
  async created(){
    await axios.get("https://raw.githubusercontent.com/donma/TaiwanAddressCityAreaRoadChineseEnglishJSON/master/CityCountyData.json")
                .then( response => this.cityapi = response.data)
    await axios.get("https://raw.githubusercontent.com/kiang/pharmacies/master/json/points.json")
                .then( response => {
                  for(let i=0; i<response.data.features.length;i++){
                    response.data.features[i].properties.opendetail=false
                  }
                  this.pharmacyapi=response.data.features
                })
    // console.log(this.pharmacyapi[0])
  },
  data(){
    return{
      pharmacyapi: [],
      cityapi: [],
      city: '',
      area: '',
      center: [24.9971354, 121.4507172],
    }
  },

  computed:{
    areafind(){
      this.area = ''
      if (this.cityapi.find(item => item.CityName == this.city ) == undefined)
      {
        return []
      }
      return this.cityapi.filter(item => item.CityName == this.city )[0].AreaList
    },
    showitems(){

      // 兩個都沒選
      if (this.area == '' & this.city == '')
      {
        return []
      }

      let result = []
      // 只選第一個
      if (this.area == '')
      {
        result = this.pharmacyapi.filter(item => item.properties.county==this.city)
        if (result.length > 0)
        {
          this.center = [result[0].geometry.coordinates[1], result[0].geometry.coordinates[0]]
        }
        return result
      }
      // 兩個都選了
      result = this.pharmacyapi.filter(item => item.properties.county==this.city).filter(item2=>item2.properties.town==this.area)
      if (result.length > 0)
      {
        this.center = [result[0].geometry.coordinates[1], result[0].geometry.coordinates[0]]
      }
      return result
    },
  },

  methods: {
    log(){
      // console.log(( this.city))
      console.log( this.cityapi.filter( item => item.CityName == this.city))
      // console.log( this.pharmacyapi.find(item => item.properties.county==this.city))
      console.log( this.cityapi.find( item => item.CityName == this.city))
    }
  }
}
</script>