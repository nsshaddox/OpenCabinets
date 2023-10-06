<template>
  <Tabs></Tabs>
</template>

<script>
  import Tabs from './components/TabObject.vue'

  export default {
    name: 'App',
    data() {
      return {
      }
    },
    components: {
      Tabs,
    }
  }
</script>

<style>
#app {
  /* font-family: Calibri, Avenir, Helvetica, Arial, sans-serif; */
  font-family: 'Trebuchet MS', Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: left;
  color: #2c3e50;
  margin-top: 60px;
}
</style>