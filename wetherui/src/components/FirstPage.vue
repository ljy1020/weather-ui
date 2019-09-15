<template>
  <div class="bodyBox">
    <div class="container">
      <!--搜索-->
      <div class="location">
        <span class="el-icon-location-outline"></span>
        <em v-show="!showSearch">{{weatherData.city}},{{weatherData.province}} </em>
        <span class="el-icon-search" @click="showSearch = true"></span>
        <span v-show="showSearch">
          <input type="text" class="searchInput" v-model="searchCityName"
                 @input="searchCity(searchCityName)">
          <span class="el-icon-close" @click="showSearch = false"></span>
          <el-card class="box-card searchContent"  v-show="searchContent">
            <div v-for="con in searchContent"  class="text item">
             <span @click="showSearch = false;getCityWeather(con.adcode)">{{con.name}}</span>
            </div>
          </el-card>
        </span>

      </div>
      <!--温度 天气-->
      <div class="po_r">
        <span class="font_120 weatherCode">{{weatherData.temperature}}</span>
        <span>
          <img :src="weatherData.weatherImageUrl" alt="">
        </span>
        <span class="font_20">{{weatherData.weather}}</span>
      </div>
      <div>
        <span>湿度 {{weatherData.humidity}}</span>
        <span>{{weatherData.winddirection}}风</span>
        <span>{{weatherData.windpower}}级</span>
      </div>
      <!--预报天气-->
      <div class="forebox">
        <table>
          <tr>
            <th>预报</th>
          </tr>
          <tr v-for="data in foreWeatherData">
            <td>{{data.date}}</td>
            <td>
              <span><img :src="data.weatherImageUrl" alt="">{{data.dayweather}}</span>
            </td>
            <td>{{data.nighttemp}}/{{data.daytemp}}</td>
            <td>{{data.daywind}}风</td>
            <td>{{data.daypower}}级</td>
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
            weatherImage : null,
            dialogVisible: false,
            weatherData :{},
            searchCityName : "",
            searchContent : null,
            showSearch : false,
            foreWeatherData : null
          }
        },
      mounted:function(){
        this.initWeatherImage();
        this.getCityWeather(null);
      },
      methods:{
        initWeatherImage(){
            this.weatherImage = new Map();
            this.weatherImage.set("晴","w0.png");
            this.weatherImage.set("阴","w2.png");
            this.weatherImage.set("多云","w1.png");
            this.weatherImage.set("中雨","w7.png");
            this.weatherImage.set("大雨","w8.png");
          },
        getCityWeather(adcode){
            this.test = {"url":require('../assets/images/weather/w1.png')};
            //test:
            /*this.weatherData = {
                "province" :
                "北京",
                "city" :
                "东城区",
                "adcode" :
                "110101",
                "weather" :
                "晴",
                "temperature" :
                "24",
                "winddirection" :
                "西南",
                "windpower" :
                "≤3",
                "humidity" :
                "56",
                "reporttime" :
                "2019-09-15 19:20:52"
            };*/
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
                let tempWeath = this.weatherData.weather;
                let temp = {};
                temp = {"weatherImageUrl":require('../assets/images/weather/'+this.weatherImage.get(tempWeath))};
                Object.assign(this.weatherData,temp);
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

          //预报天气
          axios.get(getForeUrl)
            .then((result)=>{
              let res = result.data;
              if('1' == res.code){
                this.foreWeatherData = res.data.casts;
                for (var i = 0; i< this.foreWeatherData.length ;i++ ){
                      let tempWeath = this.foreWeatherData[i].dayweather;
                      let temp = {};
                      temp = {"weatherImageUrl":require('../assets/images/weather/'+this.weatherImage.get(tempWeath))};
                      Object.assign(this.foreWeatherData[i],temp);
                }

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
  /*.searchBox{
    width: 60%;
    position: relative;
    margin: 0 auto;
  }*/
  .searchContent{
    position: absolute;
    top: 36px;
    left : 500px;
    z-index: 100;
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
  /*温度天气*/
  .po_r{
    position: relative;
  }
  .weatherCode:after{
    font-size: 48px;
    position: absolute;
    content: "°";
    top: 20px;
  }
  .font_120{
    font-size: 140px;
  }
  .font_20{
    font-size: 20px;
    font-weight: bold;
  }
  /*天气预报*/
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
  .forebox table tr{
    line-height: 40px;
  }
  .forebox td{
    width: 20%;
    text-align: center;
  }
  .forebox img{
    width: 30px;
    vertical-align: middle;
    float: left;
  }
</style>

