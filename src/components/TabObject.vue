<template>
  <div class="main-pane">
    <CNav variant="tabs" layout="fill">
      <CNavItem>
        <CNavLink class="tab-bg" @click="activeTab = 'ProjectTab'" 
          :active="activeTab === 'ProjectTab'">
          Project
        </CNavLink>
      </CNavItem>
      <CNavItem>
        <CNavLink class="tab-bg" @click="activeTab = 'TemplatesTab'" 
          :active="activeTab === 'TemplatesTab'">
          Templates
        </CNavLink>
      </CNavItem>
      <CNavItem>
        <CNavLink class="tab-bg" @click="activeTab = 'OptimizerTab'" 
          :active="activeTab === 'OptimizerTab'">
          Optimizer
        </CNavLink>
      </CNavItem>
    </CNav>

    <TemplatesView 
      v-if="activeTab === 'ProjectTab'" 
      :templates="projectTabTemplates"
      :isProjectTab="true"
      @remove-template-p="removeTemplateFromProjectTab"/>
  
    <TemplatesView 
      v-if="activeTab === 'TemplatesTab'" 
      :templates="templateTabTemplates"
      :isProjectTab="false"
      @add-template="addTemplateToProjectTab"
      @remove-template-t="removeTemplateFromTemplatesTab"/>
  
    <OptimizerView v-if="activeTab === 'OptimizerTab'" />
  </div>
</template>

<script>
  import {CNav, CNavItem, CNavLink } from '@coreui/vue'

  import TemplatesView       from './TemplateView.vue'
  import OptimizerView       from './OptimizerView.vue'
  import standardTemplates  from './templates/standardTemplates.json';

  export default {
    name: 'TabObject',
    data() {
      return {
        activeTab: 'TemplatesTab',
        projectTabTemplates: [],
        templateTabTemplates: standardTemplates,
      }
    },
    components: {
      TemplatesView,
      OptimizerView,
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

.tab-bg {
  color: rgb(109, 109, 109);
  /* background-color: rgb(176, 176, 176); */
}

.tab-bg:hover {
  color: rgb(0, 0, 0);
  background-color: rgb(216, 216, 216);
}

</style>
