<template>
  <div class="bodyBox">
    <div class="container">
      <!--搜索-->
      <div class="searchBox">
      </div>
      <div class="location">
        <span class="el-icon-location-outline"></span>
        <em>{{weatherData.city}},{{weatherData.province}} </em>
        <span class="el-icon-search" @click="showSearch = true"></span>
        <span v-show="showSearch">
        <input type="text" class="searchInput" v-model="searchCityName"
               @input="searchCity(searchCityName)">
        <span class="el-icon-close" @click="showSearch = false"></span>
        <el-card class="box-card searchContent"  v-show="searchContent">
          <div v-for="con in searchContent"  class="text item">
           <span @click="getCityWeather(con.adcode)">{{con.name}}</span>
          </div>
      </el-card>
      </span>

      </div>
      <div>
        <span>{{weatherData.temperature}}</span>
        <span>{{weatherData.weather}}</span>
      </div>
      <div>
        <span>湿度 {{weatherData.humidity}}</span>
        <span>{{weatherData.winddirection}}风</span>
        <span>{{weatherData.windpower}}级</span>
      </div>
      <!--预报天气-->
      <div class="forebox">
        <table>
          <tr v-for="data in foreWeatherData">
            <td>{{data.date}}</td>
            <td>{{data.dayweather}}</td>
            <td>{{data.nighttemp}}/{{data.daytemp}}</td>
            <td>{{data.daywind}}风</td>
            <td>{{data.nightpower}}级</td>
          </tr>
        </table>
      </div>
    </div>
  </div>

</template>

<script>
  import axios from 'axios'
    export default {
        name: "FirstPage",
        data(){
          return {
            dialogVisible: false,
            weatherData :{},
            searchCityName : "",
            searchContent : null,
            showSearch : false,
            foreWeatherData : null
          }
        },
      mounted:function(){
        this.getCityWeather(null);
      },
      methods:{
      /*  handleClose(done) {
          this.$confirm('确认关闭？')
            .then(_ => {
              done();
            })
            .catch(_ => {});
         },*/
        getCityWeather(adcode){
          //当天天气
          let getLiveUrl = "/api/weather-info/weatherLive";
          let getForeUrl = "/api/weather-info/weatherForecast";
          if(adcode){
            getLiveUrl = getLiveUrl + "?adcode=" + adcode;
            getForeUrl = getForeUrl + "?adcode=" + adcode;
            this.searchContent = null;
            this.searchCityName = null;
          }
          axios.get(getLiveUrl)
            .then((result)=>{
              let res = result.data;
              if('1' == res.code){
                this.weatherData = res.data;
              }else{
                //this.dialogVisible = true;
                this.$alert('网络出错，请稍后重试！', '提示', {
                  confirmButtonText: '确定'
                });
              }

             })
            .catch(function (error) { // 请求失败处理
              //this.dialogVisible = true;
              this.$alert('网络出错，请稍后重试！', '提示', {
                confirmButtonText: '确定'
              });
              console.log(error);
            });

          //预报天气
          axios.get(getForeUrl)
            .then((result)=>{
              let res = result.data;
              if('1' == res.code){
                this.foreWeatherData = res.data.casts;
              }else{
                this.$alert('网络出错，请稍后重试！', '提示', {
                  confirmButtonText: '确定'
                });
              }

            })
            .catch(function (error) { // 请求失败处理
              //this.dialogVisible = true;
              this.$alert('网络出错，请稍后重试！', '提示', {
                confirmButtonText: '确定'
              });
              console.log(error);
            });
        },
        searchCity(name){
          debugger;
          if('0' == name.length){
            return;
          }
          let getUrl = "/api/weather-city/getCityInfo?cityName=" + name;
          axios.get(getUrl)
            .then((result)=>{
              let res = result.data;
              if('1' == res.code){
                if (res.data.length>0){
                  this.searchContent = res.data;
                }else{
                  this.searchContent = null;
                }

              }else{
                this.$alert('网络出错，请稍后重试！', '提示', {
                  confirmButtonText: '确定'
                });
              }

            })
            .catch(function (error) { // 请求失败处理
              this.$alert('网络出错，请稍后重试！', '提示', {
                confirmButtonText: '确定'
              });
              console.log(error);
            });
        }
      }
    }
</script>
<style>
  .bodyBox{
    background: url("../assets/images/day_1.jpg") no-repeat;
    background-size:cover;
  }
  .container{
    height: 1519px;
    width: 1000px;
    margin: 0px auto;
    padding: 10px 20px;
    color: white;
  }
  .searchBox{
    width: 60%;
    position: relative;
    margin: 0 auto;
  }
  .searchContent{
    position: absolute;
    top: 50px;
    left : 420px;
/*
    background-color: aliceblue;
*/
    background:  rgba(0, 0, 0, .3);
    border:none;
    color: white;
  }
  .location em {
    font-weight: normal;
    white-space: nowrap;；
    overflow: hidden;
    font-size: 20px;
    line-height: 26px;
    height: 26px;
    font-style: normal;
    padding-right: 10px;
  }
  .searchInput{
   /* opacity: 1;*/
    width: 140px;
    overflow: hidden;
    height: 25px;
    line-height: 25px;
    padding: 0;
    border-bottom: 1px solid rgba(255, 255, 255, .2);
    border-width: 0 0 1px 0;
    outline: none;
    background: none;
    margin: 0 0 0 10px;
    font-size: 14px;
    color: #fff;
  }
  /**/
  .forebox{
    background:  rgba(0, 0, 0, .3);
    border:none;
    width: 600px;
    margin: 20px 0px;
    padding: 10px;
  }
  .forebox table{
    width: 100%;
  }
  .forebox td{
    width: 20%;
    text-align: center;
  }
</style>

