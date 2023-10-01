<template>
  <!-- <CButton color="primary" @click="() => { visibleStaticBackdropDemo = true }">Launch demo modal</CButton> -->
  <CModal backdrop="static" :visible="visibleStaticBackdropDemo" @close="() => { this.$emit('close-modal') }">
    <CModalHeader>
      <CModalTitle>Modal title</CModalTitle>
    </CModalHeader>
    <CModalBody>
      <!-- <div>"Name": <input v-text="{{ this.template.Name }}" /></div> -->
      
        <div v-for="row in template.Components" :key="row">
          <!-- <div v-if="row[0] !== 0"> -->
            {{ row }}
          <!-- </div> -->
        </div>
    </CModalBody>
    <CModalFooter>
      <CButton color="secondary" @click="() => { this.$emit('close-modal') }">
        Close
      </CButton>
      <CButton color="primary">Save changes</CButton>
    </CModalFooter>
  </CModal>
</template>

<script>
    import {  
      CButton, 
      CModal, 
      CModalTitle,
      CModalHeader,
      CModalBody, 
      CModalFooter,
    } from '@coreui/vue';

  export default {
    name: "EditTemplateModal",
    props: {
      template: {
        type: Array,
        default: () => []
      },
      visibleStaticBackdropDemo: {
        type: Boolean,
        default: false,
      }
    },
    data() {
      return { 
        // visibleStaticBackdropDemo: false,
      }
    },
    components: {
      CButton, 
      CModal, 
      CModalTitle,
      CModalHeader, 
      CModalBody, 
      CModalFooter,
    },
  }
</script>

<style scoped>
</style>
