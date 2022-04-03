<template>
  <div class="mask">
    <div class="sidebar w-1/5 h-screen bg-slate-300 p-2 shadow-inner shadow-slate-100 ">
      <div class="selectors w-full flex overflow-hidden">
        <select name="city" v-model="city" class="flex-1 text-center border-solid border-2 border-slate-600 rounded-md mr-2 py-1"> 
          <option value="" disabled >請選擇城市</option>
          <option :value="c.CityName" :key="c.CityName" v-for=" c in cityapi"> {{c.CityName}} </option>
        </select>
        <select name="area"  v-model="area" class="flex-1 text-center border-solid border-2 border-slate-600 rounded-md"> 
          <option value="" disabled >請選擇區域</option>
          <option :value="a.AreaName" :key="a.AreaName" v-for=" a in areafind"> {{a.AreaName}}</option>
          <!-- <option :value="a.AreaName" :key="a.AreaName" v-for=" a in cityapi.find( item => item.CityName == city).AreaList"> {{a.AreaName}}</option> -->
        </select>
      </div>
      <div class="wrap flex flex-col overflow-y-auto">
        <div class="item mt-2 p-2 bg-slate-500 rounded-md text-slate-200" :key="pharmacy" v-for="pharmacy in pharmacyapi">
          <div class="name text-xl underline decoration-pink-800">{{pharmacy.properties.name}}</div>
          <div class="txt ">
            <div class="phone text-xs"><fa :icon='["fas" , "phone"]' class="mr-1.5 text-[10px]"/>{{pharmacy.properties.phone}}</div>
            <div class="addr text-xs flex items-center">
              <fa :icon='["fas" , "house-medical"]' class="mr-1.5 text-[10px]"/>
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
          <div class="detailicon py-1 mt-1 text-xs text-center rounded-sm bg-slate-300 text-slate-700 cursor-zoom-in no-underline hover:underline" @click="opendetail=!opendetail" :class="{'opendetail_box': opendetail}">營業時間</div>
          <div class="detail text-xs p-1 text-center bg-slate-300 text-slate-700 rounded-b-sm" v-show="opendetail">{{pharmacy.properties.available}}</div>

        </div>
      </div>
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
  async fetch(){
    await axios.get("https://raw.githubusercontent.com/kiang/pharmacies/master/json/points.json")
                .then( response => this.pharmacyapi = response.data.features)
    await axios.get("https://raw.githubusercontent.com/donma/TaiwanAddressCityAreaRoadChineseEnglishJSON/master/CityCountyData.json")
                .then( response => this.cityapi = response.data)
    console.log(this.cityapi)

  },
  data(){
    return{
      pharmacyapi: [],
      cityapi: [],
      city: '',
      area: '',
      opendetail: false,

    }
  },

  computed:{
    areafind() {
      if (this.cityapi.find(item => item.CityName == this.city ) == undefined)
      {
        return [];
      }
      return this.cityapi.find(item => item.CityName == this.city ).AreaList
    }
  },

  methods: {
    log() {
      // console.log(this.city)
      // console.log(this.area)
    }
  }
}
</script>