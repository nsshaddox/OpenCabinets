<template>

  <EditTemplateModal :editData="editData" @save-changes="saveChanges" @close-modal="closeModal()"></EditTemplateModal>
  <div class="grid-container">
    <div class="row-containers" v-for="(template, index) in templates" :key="template.Name">
      <CButtonGroup class="button-set" size="sm" role="group" aria-label="Button group with nested dropdown">
        <CButton @click="toggleTable(index)" class="toggle-table" :color="buttonColor">{{ getButtonText(index) }}</CButton>
        <CDropdown  variant="btn-group">
          <CDropdownToggle class="dropdown" :color="buttonColor"></CDropdownToggle>
          <CDropdownMenu>
            <CDropdownItem v-if="!isProjectTab" @click="addTemplate(index)">Add</CDropdownItem>
            <CDropdownItem @click="editClicked(template, index)">Edit</CDropdownItem>
            <CDropdownDivider/>
            <CDropdownItem @click="removeTemplate(index)">Remove</CDropdownItem>
          </CDropdownMenu>
        </CDropdown>
      </CButtonGroup>
      <table v-if="visibleTables[index]">
        <tr>
          <th>Q</th>
          <th>Part</th>
          <th>Width</th>
          <th>Length</th>
          <th>Thick</th>
          <th>Material</th>
          <th>Notes</th>
        </tr>
        <tr v-for="row in template.Components" :key="row">
          <template v-if="row[0] !== 0">
            <td v-for="(cell, i) in row" :key="i">
              {{ formatCell(cell, i) }}
            </td>
          </template>
        </tr>
      </table>
    </div>
  </div>
</template>

<script>
  import { 
    CDropdown, 
    CDropdownToggle, 
    CDropdownItem, 
    CDropdownMenu, 
    CDropdownDivider, 
    CButton,CButtonGroup 
  } from '@coreui/vue';

  import EditTemplateModal from './EditTemplateModal.vue'

  export default {
    name: "TemplatesView",
    props: {
      isProjectTab: {
        type: Boolean,
        default: false,
      },
      templates: {
        type: Array,
        default: () => [],
      },
    },
    data() {
      return {
        visibleTables: new Array(this.templates.length).fill(true),
        buttonColor: "info",
        timesSorted: 0,
        editData: {
          template: {},
          visibleStaticBackdropDemo: false,
          index: 0,
        },
      }
    },
    created () {
      if (this.isProjectTab) {
        this.buttonColor = 'secondary'
      } else {
        this.buttonColor = 'dark'
      }
    },
    emits: [
      'remove-template-p',
      'remove-template-t',
      'add-template',
      'save-changes',
    ],
    components: {
      CDropdown, 
      CDropdownToggle, 
      CDropdownItem, 
      CDropdownMenu,
      CDropdownDivider, 
      CButton,
      CButtonGroup,
      EditTemplateModal,
    },
    methods: {
      /**
       * Formats a cell based on the column index.
       * @param {number} cell - The cell value.
       * @param {number} col - The column index.
       * @returns {string|number} The formatted cell value.
       */
      formatCell(cell, col) {
        if (col < 2 || col > 4) return cell;
        return cell.toFixed(2).toString();
      },

      /**
       * Toggles the visibility of a table.
       * @param {number} index - The index of the template.
       */
      toggleTable(index) {
        this.visibleTables[index] = !this.visibleTables[index];
        console.log(this.visibleTables[index]);
      },

      /**
       * Removes a template.
       * @param {number} index - The index of the template.
       */
      removeTemplate(index) {
        if (this.isProjectTab){
          this.$emit('remove-template-p', index);
        } else {
          this.$emit('remove-template-t', index);
        }
      },

      /**
       * Adds a template.
       * @param {number} index - The index of the template.
       */
      addTemplate(index) {
        this.$emit('add-template', this.templates[index]);
      },

      /**
       * Generates button text based on template metadata.
       * @param {number} index - The index of the template.
       * @returns {string} The generated button text.
       */
      getButtonText(index) {
        const data = this.templates[index].MetaData;
        const spacer = ' || ';
        let result = '';

        result += data.Quantity + spacer;
        result += this.templates[index].Name + spacer;
        result += data.Style + spacer;
        result += `W: ${data.Width}` + spacer;
        result += `D: ${data.Depth}` + spacer;
        result += `H: ${data.Height}` + spacer;
        result += `Inner: ${data.InnerMaterial}` + spacer;
        result += `Outer: ${data.OuterMaterial}`;

        return result;
      },

      /**
       * Closes the edit modal
       */
      closeModal() {
        this.editData.visibleStaticBackdropDemo = false;
      },

      /**
       * Handles the click event for editing a template.
       * @param {object} template - The template to be edited.
       * @param {number} index - The index of the template.
       */
      editClicked(template, index) {
        this.editData.visibleStaticBackdropDemo = true;
        this.editData.template = template;
        this.editData.index = index;
      },

      /**
       * Saves changes to a template stored in TabObject.vue.
       * @param {object} template - The modified template.
       * @param {number} index - The index of the template.
       */
      saveChanges(template, index) {
        this.$emit('save-changes', template, index);
      }
    },
  }
</script>

<style scoped>
.grid-container {
  max-width: 1200px;
  min-width: 750px;
  /* margin: 10px; */
  /* width: 100%; */
}

.button-set {
  display: flex;
  /* justify-content: space-evenly; */
  margin-bottom: 2px;
  margin-top: 2px;
}

.toggle-table {
  height: 25px;
  display: flex;
  align-items: center;
  font-weight: bold;
  color: white;
  /* border: 1px solid rgb(98, 57, 223); */
  border-radius: 0px;
}

.toggle-table:hover {
  color: white;
  /* border: 1px solid blue; */
}

.dropdown {
  height: 25px;
  display: flex;
  align-items: center;
  color: white;
  margin-left: 3px;
  /* border: 1px solid black; */
  border-radius: 0px;
}

/* Table CSS */
table {
  border-collapse: collapse;
  table-layout: fixed;
  width: 100%; 
}

td, th {
  border: 1px solid black;
  padding: 8px;
  word-wrap: break-word;
  overflow: hidden; 
  max-width: 100%; 
  line-height: 5px;
}

th {
  background-color: rgb(173, 205, 251);
  /* border: 2px solid black; */
}

th:nth-child(1),
td:nth-child(1) {
  width: 40px;
  text-align: right;
}
th:nth-child(3),
td:nth-child(3),
th:nth-child(4),
td:nth-child(4),
th:nth-child(5),
td:nth-child(5) {
  width: 70px;
}

td:nth-child(3),
td:nth-child(4),
td:nth-child(5) {
  text-align: right;
}

td:nth-child(2),
td:nth-child(6),
td:nth-child(7) {
  text-align: left;
}

</style>
