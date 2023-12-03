<template>
  <template v-if="printData.isPrinting">
    <PrintModal :printData="printData" @close-print="closePrint"></PrintModal>
  </template>

  <template v-else>
    {{ sortTemplateParts(this.projectTabTemplates) }}
    {{ sortTemplateParts(this.templateTabTemplates) }}
    <div class="main-pane">
      <CNav variant="tabs" layout="justified">
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
        <CNavItem style="padding-right: 10px;">
          <CNavLink class="tab-bg" @click="activeTab = 'OptimizerTab'" 
            :active="activeTab === 'OptimizerTab'">
            Optimizer
          </CNavLink>
        </CNavItem>
        <CNavItem>
          <CButton 
            style="padding-top: 0px; padding-bottom: 0px; text-align: start;" 
            @click="setPrintData" color="primary">
            Print Preview
          </CButton>
        </CNavItem>
      </CNav>

      <!-- <KeepAlive> -->
        <TemplatesView 
          v-if="activeTab === 'ProjectTab'" 
          :templates="projectTabTemplates"
          :isProjectTab="true"
          @remove-template-p="removeTemplateFromProjectTab"
          @save-changes="saveChangesFromProjectTab"/>
      <!-- </KeepAlive> -->
    
      <!-- <KeepAlive> -->
        <TemplatesView 
          v-if="activeTab === 'TemplatesTab'" 
          :templates="templateTabTemplates"
          :isProjectTab="false"
          @add-template="addTemplateToProjectTab"
          @remove-template-t="removeTemplateFromTemplatesTab"
          @save-changes="saveChangesFromTemplatesTab"/>
      <!-- </KeepAlive> -->
    
      <OptimizerView v-if="activeTab === 'OptimizerTab'" 
        :templates="projectTabTemplates"/>

    </div>
  </template>

</template>

<script>
  import { CNav, CNavItem, CNavLink, CButton } from '@coreui/vue'

  import TemplatesView        from './TemplateView.vue'
  import OptimizerView        from './OptimizerView.vue'
  import standardTemplates    from './templates/standardTemplates.json';
  import PrintModal           from './PrintModal.vue';

  export default {
    name: 'TabObject',
    data() {
      return {
        activeTab: 'TemplatesTab',
        projectTabTemplates: [],
        templateTabTemplates: standardTemplates,
        printData: {
          isPrinting: false,
          printableTemplates: [],
        }
      }
    },
    components: {
      TemplatesView,
      OptimizerView,
      CNav, CNavItem, CNavLink,
      CButton,
      PrintModal,
    },
    methods: {
      closePrint() {
        this.printData.isPrinting = false;
      },
      setPrintData() {
        if (this.activeTab === 'TemplatesTab') {
          this.printData.printableTemplates = this.templateTabTemplates;
        } else if (this.activeTab === 'ProjectTab') {
          this.printData.printableTemplates = this.projectTabTemplates;
        } else {
          this.printData.printableTemplates = []
          window.print()
          return
        }
        this.printData.isPrinting = true;
      },
      addTemplateToProjectTab(template) {
        console.log('TabObject: Adding ', template.Name);
        this.projectTabTemplates.push(template);
      },
      removeTemplateFromTemplatesTab(index) {
        console.log('TabObject: Removing', this.templateTabTemplates[index], 'from Templates');
        this.templateTabTemplates.splice(index, 1);
      },
      removeTemplateFromProjectTab(index) {
        console.log('TabObject: Removing', this.templateTabTemplates[index], 'from Project');
        this.projectTabTemplates.splice(index, 1);
      },
      saveChangesFromProjectTab(template, index) {
        this.projectTabTemplates[index] = JSON.parse(JSON.stringify(template));
      },
      saveChangesFromTemplatesTab(template, index) {
        this.templateTabTemplates[index] = JSON.parse(JSON.stringify(template));
      },
      sortTemplateParts(templates) {
        for (let template of templates){
          const innerMaterial = template.MetaData.InnerMaterial;
          this.tempTemplate = template;
          this.tempTemplate.Components.sort((a, b) => {
            // Compare materials first
            if (a[5] !== b[5]) {
              if (a[5] === innerMaterial) return -1;
              if (b[5] === innerMaterial) return 1;
              return a[5].localeCompare(b[5]);
            }
            
            // If materials are equal, compare thickness (greater first)
            if (a[4] !== b[4]) {
              return b[4] - a[4];
            }
            
            // If thicknesses are equal, compare lengths (greater first)
            if (a[3] !== b[3]) {
              return b[3] - a[3];
            }
            
            // If lengths are equal, compare widths (greater first)
            return b[2] - a[2];
          });
        }
      },
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
  background-color: rgb(235, 235, 235);
}

.tab-bg:hover {
  color: rgb(0, 0, 0);
  background-color: rgb(216, 216, 216);
}

</style>
