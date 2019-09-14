<template>
  <div class="container">
    <div>
      <span class="el-icon-location-outline"></span>
      <span>{{nowCity}}</span>
    </div>
    <div></div>
    <div></div>
  </div>
</template>

<script>
  import axios from 'axios'
    export default {
        name: "FirstPage",
        data(){
          return {
            nowCity:"雨花区"
          }
        },
      mounted:function(){
        this.getCityWeather();
      },
      methods:{
        getCityWeather(){
          axios.get("/api")
            .then((result)=>{
              var res = result.data;
              alert("ok");
             })
            .catch(function (error) { // 请求失败处理
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
</style>

