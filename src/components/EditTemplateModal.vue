<template>
  <!-- <CButton color="primary" @click="() => { visibleStaticBackdropDemo = true }">Launch demo modal</CButton> -->
  <CModal size="lg" backdrop="static" :visible="modalData.visibleStaticBackdropDemo" @close="closeModal">
    <CModalHeader>
      <CModalTitle>Modify Cabinet</CModalTitle>
    </CModalHeader>
    <CModalBody>
      {{ copyTemplate() }}
      <!-- {{ modifiedTemplate }} -->
      <CInputGroup class="mb-3">
        <CInputGroupText id="cabinet-name">Name</CInputGroupText>
        <CFormInput 
          v-model="modifiedTemplate.Name" 
          :placeholder="this.modifiedTemplate.Name" 
          aria-label="name" 
          aria-describedby="cabinet-name"/>
        <CInputGroupText id="cabinet-name">Quantity</CInputGroupText>
        <CFormInput 
          v-model="modifiedTemplate.MetaData.Quantity" 
          :placeholder="this.modifiedTemplate.MetaData.Quantity" 
          aria-label="quantity" 
          aria-describedby="cabinet-quantity"/>
      </CInputGroup>

      <CInputGroup class="mb-3">
        <CInputGroupText id="cabinet-style">Style</CInputGroupText>
        <CFormInput 
          v-model="modifiedTemplate.MetaData.Style" 
          :placeholder="this.modifiedTemplate.MetaData.Style" 
          aria-label="style" 
          aria-describedby="cabinet-style"/>
      </CInputGroup>

      <CInputGroup class="mb-3">
        <CInputGroupText id="cabinet-width">Width</CInputGroupText>
        <CFormInput
          v-model="modifiedTemplate.MetaData.Width" 
          :placeholder="this.modifiedTemplate.MetaData.Width"
          aria-label="width"
          aria-describedby="cabinet-width"/>
        <CInputGroupText id="cabinet-depth">Depth</CInputGroupText>
        <CFormInput
          v-model="modifiedTemplate.MetaData.Depth" 
          :placeholder="this.modifiedTemplate.MetaData.Depth" 
          aria-label="depth" 
          aria-describedby="cabinet-depth"/>
        <CInputGroupText id="cabinet-height">Height</CInputGroupText>
        <CFormInput 
          v-model="modifiedTemplate.MetaData.Height" 
          :placeholder="this.modifiedTemplate.MetaData.Height" 
          aria-label="height" 
          aria-describedby="cabinet-height"/>
      </CInputGroup>

      <CInputGroup class="mb-3">
        <CInputGroupText id="cabinet-description">Description</CInputGroupText>
        <CFormInput 
          v-model="modifiedTemplate.MetaData.Description"
          :placeholder="this.modifiedTemplate.MetaData.Description" 
          aria-label="description" 
          aria-describedby="cabinet-description"/>
      </CInputGroup>

      <CInputGroup class="mb-3">
        <CInputGroupText id="cabinet-inner-material">Inner Material</CInputGroupText>
        <CFormInput 
          v-model="modifiedTemplate.MetaData.InnerMaterial" 
          :placeholder="this.modifiedTemplate.MetaData.InnerMaterial" 
          aria-label="inner-material" 
          aria-describedby="cabinet-inner-material"/>
      </CInputGroup>

      <CInputGroup class="mb-3">
        <CInputGroupText id="cabinet-outer-material">Outer Material</CInputGroupText>
        <CFormInput 
          v-model="modifiedTemplate.MetaData.OuterMaterial" 
          :placeholder="this.modifiedTemplate.MetaData.OuterMaterial" 
          aria-label="style" 
          aria-describedby="cabinet-outer-material"/>
      </CInputGroup>
      
      <CFormSwitch @change="testing"  label="Auto Size Components" checked/>
      
    </CModalBody>
    <CModalFooter>
      <CButton color="secondary" @click="closeModal">
        Close
      </CButton>
      <CButton @click="saveChanges()" color="primary">Save changes</CButton>
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
      CInputGroup,
      CInputGroupText,
      CFormInput,
      CFormSwitch
    } from '@coreui/vue';

  export default {
    name: "EditTemplateModal",
    props: {
      modalData: {
        type: Object,
        default: () => ({})
      },
    },
    data() {
      return { 
        modifiedTemplate: null,
        isAutoChecked: true,
      }
    },
    emits: ['close-modal', 'save-changes'],
    components: {
      CButton, 
      CModal, 
      CModalTitle,
      CModalHeader, 
      CModalBody, 
      CModalFooter,
      CInputGroup,
      CInputGroupText,
      CFormInput,
      CFormSwitch,
    },
    methods: {
      testing() {
        this.isAutoChecked = !this.isAutoChecked
      },
      closeModal() {
        this.$emit('close-modal')
      },
      saveChanges() {
        if (this.isAutoChecked) {
          const QUANTITY = this.modifiedTemplate.MetaData.Quantity;
          const WIDTH = this.modifiedTemplate.MetaData.Width;
          const DEPTH = this.modifiedTemplate.MetaData.Depth;
          const HEIGHT = this.modifiedTemplate.MetaData.Height;
          const INNER_MATERIAL = this.modifiedTemplate.MetaData.InnerMaterial;
          const OUTER_MATERIAL = this.modifiedTemplate.MetaData.OuterMaterial;
          const THICK_BOX = 0.75;   //add this to the metadata
          const THICK_BACK = 0.25;  //add this to the metadata
          
          for ( let component of this.modifiedTemplate.Components) {
            switch (component[1]) {
              case 'Side Panel':
                component[0] = 2 * QUANTITY;    //Quantity
                component[2] = DEPTH;           //Width
                component[3] = HEIGHT;          //Length
                component[4] = THICK_BOX;       //Thick
                component[5] = INNER_MATERIAL;  //InnerMaterial
                break;
              case 'Bottom Panel':
                component[0] = QUANTITY;        //Quantity
                component[2] = DEPTH;           //Width
                component[3] = WIDTH - 1.5;     //Length
                component[4] = THICK_BOX;       //Thick
                component[5] = INNER_MATERIAL;  //InnerMaterial
                break;
              // case 'Top Panel': // Same as BOTTOM
              //   break;
              case 'Back Panel':
                component[0] = QUANTITY;        //Quantity
                component[2] = WIDTH - 1;       //Width
                component[3] = HEIGHT - 1;      //Length
                component[4] = THICK_BACK;      //Thick
                component[5] = INNER_MATERIAL;  //InnerMaterial
                break;
              case 'Shelves':
                component[0] = 2 * QUANTITY;    //Quantity
                component[2] = DEPTH - 1.25;    //Width
                component[3] = WIDTH - 1.75;    //Length
                component[4] = THICK_BOX;       //Thick
                component[5] = INNER_MATERIAL;  //InnerMaterial
                break;
              case 'Webs':
                component[0] = QUANTITY;        //Quantity
                component[2] = 8.75;            //Width
                component[3] = WIDTH - 1.5;     //Length
                component[4] = THICK_BOX;       //Thick
                component[5] = INNER_MATERIAL;  //InnerMaterial
                break;
              case 'Door Panel':
                component[0] = 2 * QUANTITY;          //Quantity
                component[2] = (WIDTH - 0.375) / 2;   //Width
                component[3] = WIDTH - 1.5;           //Length
                component[4] = THICK_BOX;             //Thick
                component[5] = OUTER_MATERIAL;        //Material
                break;
            }
          }
        }
        this.$emit('save-changes', this.modifiedTemplate, this.modalData.index);
        this.closeModal();
      },
      copyTemplate() {
        console.log(this.modalData.template.Components);
        this.modifiedTemplate = JSON.parse(JSON.stringify(this.modalData.template));
        console.log(this.modifiedTemplate.Components);
      }
    },
  }
</script>

<style scoped>
</style>
