<template>
  <div id="app">
    <!-- <img src="./assets/timg.jpg">-->
    <router-view />

  </div>
</template>

<script>

  export default {
    name: 'App',
    data(){
      return{

      }
    },
    methods:{

    },
    mounted() {
      //刷新的时候token会消失  重新获取token
      window.addEventListener("unload",()=>{
        sessionStorage.setItem("state",JSON.stringify(this.$store.state))
      })
    }
  }
</script>

<style>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;

    color: #2c3e50;
    /*  margin-top: 60px;*/
  }
  html,body,#app{
    height: 100%;
    width: 100%;
  }
</style>
