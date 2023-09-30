<template>
  <div class="main-pane">
    <CNav variant="tabs" layout="fill">
      <CNavItem >
        <CNavLink @click="activeTab = 'ProjectTab'" :active="activeTab === 'ProjectTab'">Project</CNavLink>
      </CNavItem>
      <CNavItem>
        <CNavLink @click="activeTab = 'TemplatesTab'" :active="activeTab === 'TemplatesTab'">Templates</CNavLink>
      </CNavItem>
      <CNavItem>
        <CNavLink @click="activeTab = 'OptimizerTab'" :active="activeTab === 'OptimizerTab'">Optimizer</CNavLink>
      </CNavItem>
    </CNav>
  
    <ProjectTab 
      v-if="activeTab === 'ProjectTab'" 
      :templates="projectTabTemplates"
      @remove-template-p="removeTemplateFromProjectTab"/>
  
    <TemplatesTab 
      v-if="activeTab === 'TemplatesTab'" 
      :templates="templateTabTemplates"
      @add-template="addTemplateToProjectTab"
      @remove-template-t="removeTemplateFromTemplatesTab"/>
  
    <OptimizerTab v-if="activeTab === 'OptimizerTab'" />
  </div>
</template>

<script>
  import {CNav, CNavItem, CNavLink } from '@coreui/vue'

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
      OptimizerTab,
      CNav, CNavItem, CNavLink,
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
.main-pane {
  max-width: 1200px;
  min-width: 800px;
  border: 1px solid lightgray;
  border-radius: 6px;
  margin: 10px;
  padding: 10px 10px;
}

</style>
