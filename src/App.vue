<!--
 * @Descripttion: 
 * @version: 
 * @Author: silencetea
 * @Date: 2020-03-14 19:58:55
 * @LastEditors: silencetea
 * @LastEditTime: 2020-03-15 14:36:10
 -->
<template>
  <div id="app">
    <VueVideo />
  </div>
</template>

<script>
import VueVideo from './components/VueVideo.vue'

export default {
  name: 'App',
  components: {
    VueVideo
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  /* text-align: center; */
  color: #2c3e50;
  margin-top: 60px;
}
</style>
