<template lang="pug">
  .maskmap
    .searchbar.w-96.h-screen.bg-slate-300
      select(v-model="city")
        option.text-center(disabled value="") 請選擇地區
        option.text-center A

</template>
<style lang="sass">
  .maskmap
    .searchbar
      .item
        width: 80%
</style>
<script>
import axios from 'axios'
export default {
  async fetch(){
    await axios.get("https://raw.githubusercontent.com/donma/TaiwanAddressCityAreaRoadChineseEnglishJSON/master/AllData.json")
                .then( response => this.mapapi = response)
    // await axios.get("https://raw.githubusercontent.com/donma/TaiwanAddressCityAreaRoadChineseEnglishJSON/master/AllData.json")
    //             .then( response => this.cityapi = response)
    console.log(this.mapapi)
  },
  data(){
    return{
      mapapi: [],
      cityapi: [],
    }
  }
}
</script>
