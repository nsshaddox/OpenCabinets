<template>
  <button @click="activeTab = 'ProjectTab'">Project</button>
  <button @click="activeTab = 'TemplatesTab'">Templates</button>
  <button @click="activeTab = 'OptimizerTab'">Optimizer</button>

  <KeepAlive>
    <component :is="activeTab" />
  </KeepAlive>
</template>

<script>
  import ProjectTab   from './ProjectTab.vue'
  import TemplatesTab from './TemplatesTab.vue'
  import OptimizerTab from './OptimizerTab.vue'

  export default {
    name: 'TabObject',
    data() {
      return {
        activeTab: 'TemplatesTab' //Component name
      }
    },
    components: {
      ProjectTab,
      TemplatesTab,
      OptimizerTab
    }
  }
</script>

<style scoped>

</style>
