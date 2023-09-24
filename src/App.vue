<template>
  <button @click="activeTab = 'ProjectTab'">Project</button>
  <button @click="activeTab = 'TemplatesTab'">Templates</button>
  <button @click="activeTab = 'OptimizerTab'">Optimizer</button>

  <KeepAlive>
    <component :is="activeTab" />
  </KeepAlive>
</template>

<script>
  import ProjectTab from './components/ProjectTab.vue'
  import TemplatesTab from './components/TemplatesTab.vue'
  import OptimizerTab from './components/OptimizerTab.vue'

  export default {
    name: 'App',
    data() {
      return {
        activeTab: 'ProjectTab' //Component name
      }
    },
    components: {
      ProjectTab,
      TemplatesTab,
      OptimizerTab
    }
  }
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

</style>
