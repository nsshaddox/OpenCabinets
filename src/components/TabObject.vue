<template>
  <button @click="activeTab = 'ProjectTab'">Project</button>
  <button @click="activeTab = 'TemplatesTab'">Templates</button>
  <button @click="activeTab = 'OptimizerTab'">Optimizer</button>

  <ProjectTab v-if="activeTab === 'ProjectTab'" 
    :templates="projectTabTemplates"
    @remove-template-p="removeTemplateFromProjectTab"/>

  <TemplatesTab v-if="activeTab === 'TemplatesTab'" 
    :templates="templateTabTemplates"
    @add-template="addTemplateToProjectTab"
    @remove-template-t="removeTemplateFromTemplatesTab"/>

  <OptimizerTab v-if="activeTab === 'OptimizerTab'" />

</template>

<script>
  import ProjectTab   from './ProjectTab.vue'
  import TemplatesTab from './TemplatesTab.vue'
  import OptimizerTab from './OptimizerTab.vue'

  import standardTemplates  from './templates/standardTemplates.json';

  export default {
    name: 'TabObject',
    data() {
      return {
        activeTab: 'TemplatesTab', //Component name
        projectTabTemplates: [],
        templateTabTemplates: standardTemplates,
      }
    },
    components: {
      ProjectTab,
      TemplatesTab,
      OptimizerTab
    },
    methods: {
      addTemplateToProjectTab(template) {
        console.log('TabObject: Adding ', template.Name);
        this.projectTabTemplates.push(template);
      },
      removeTemplateFromTemplatesTab(index) {
        this.templateTabTemplates.splice(index, 1);
        console.log(index);
      },
      removeTemplateFromProjectTab(index) {
        this.projectTabTemplates.splice(index, 1);
      }
    },
  }
</script>

<style scoped>

</style>
