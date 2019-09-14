<template>
  <div class="container">
    <!--搜索-->
    <div class="searchBox">
     <!-- <el-input v-model="searchCityName" placeholder="请输入城市"></el-input>
      <el-button icon="el-icon-search" circle @click="searchWeather(searchCityName)"></el-button>
    -->
      <el-input
        placeholder="请输入城市"
        suffix-icon="el-icon-search"
        v-model="searchCityName">
      </el-input>
      <div class="searchContent" v-show="searchContent">
        sddsd
      </div>
    </div>
    <div>
      <span class="el-icon-location-outline"></span>
      <span>{{weatherData.city}}</span>
      <span>{{weatherData.province}}</span>
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
    <div></div>
    <el-dialog
      title="提示"
      :visible.sync="dialogVisible"
      width="30%"
      :before-close="handleClose">
      <span>网络出错，请稍后重试！</span>
      <span slot="footer" class="dialog-footer">
    <el-button @click="dialogVisible = false">取 消</el-button>
    <el-button type="primary" @click="dialogVisible = false">确 定</el-button>
  </span>
    </el-dialog>
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
            searchCityName : ""
          }
        },
      mounted:function(){
        this.getCityWeather();
      },
      methods:{
        handleClose(done) {
          this.$confirm('确认关闭？')
            .then(_ => {
              done();
            })
            .catch(_ => {});
         },
        getCityWeather(){
          axios.get("/api/weather-info/weatherLive")
            .then((result)=>{
              var res = result.data;
              if('1' == res.code){
                this.weatherData = res.data;
              }else{
                this.dialogVisible = true;
              }

             })
            .catch(function (error) { // 请求失败处理
              this.dialogVisible = true;
              console.log(error);
            });
        },
        searchWeather(name){
          debugger;
          if('0' == name.length){
            this.$alert('请输入查询城市', '提示', {
              confirmButtonText: '确定'/*,
              callback: action => {
                this.$message({
                  type: 'info',
                  message: `action: ${ action }`
                });
              }*/
            });
          }
          var getUrl = "/api/weather-city/getCityInfo?cityName=" + name;
          axios.get(getUrl)
            .then((result)=>{
              var res = result.data;
              /*if('1' == res.code){
                this.weatherData = res.data;
              }else{
                this.dialogVisible = true;
              }*/

            })
            .catch(function (error) { // 请求失败处理
              this.dialogVisible = true;
              console.log(error);
            });
        }
      }
    }
</script>
<style>
  .container{
    width: 1000px;
    margin: 0px auto;
    background-color:#e4e5e6;
  }
  .searchBox{
    width: 60%;
    position: relative;
  }
  .searchContent{
    position: absolute;
    top: 40px;
    background-color: aliceblue;
  }
</style>

