<template>
  <EditTemplateModal :template="tempTemplate" :visibleStaticBackdropDemo="isModalVisible" @close-modal="closeModal()"></EditTemplateModal>
  <div class="grid-container">
    <div class="row-containers" v-for="(template, index) in templates" :key="template.Name">
      {{ sortTemplateParts(template) }}
      <CButtonGroup class="button-set" size="sm" role="group" aria-label="Button group with nested dropdown">
        <CButton @click="toggleTable(index)" class="toggle-table" :color="buttonColor">{{ getButtonText(index) }}</CButton>
        <CDropdown  variant="btn-group">
          <CDropdownToggle class="dropdown" :color="buttonColor"></CDropdownToggle>
          <CDropdownMenu>
            <CDropdownItem v-if="!isProjectTab" @click="addTemplate(index)">Add</CDropdownItem>
            <CDropdownItem @click="this.isModalVisible = true">Edit</CDropdownItem>
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
        <tr v-for="row in tempTemplate.Components" :key="row">
          <template v-if="row[0] !== 0">
            <td v-for="(cell, index) in row" :key="index">
              <!-- Format the cells to display 2 decimal places even if they are whole numbers -->
              {{ index > 1 && typeof cell === 'number' ? (cell % 1 === 0 ? cell + '.00' : cell.toFixed(2)) : cell }}
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
    name: "TemplatesView",
    data() {
      return {
        visibleTables: new Array(this.templates.length).fill(true),
        buttonColor: "info",
        tempTemplate: [],
        isModalVisible: false
      }
    },
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
      toggleTable(index) {
        this.visibleTables[index] = !this.visibleTables[index];
        console.log(this.visibleTables[index]);
      },
      removeTemplate(index) {
        if (this.isProjectTab){
          this.$emit('remove-template-p', index);
        } else {
          this.$emit('remove-template-t', index);
        }
      },
      addTemplate(index) {
        this.$emit('add-template', this.templates[index]);
      },
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
      sortTemplateParts(template, innerMaterial) {
        console.log("sort the templates", template);
        this.tempTemplate = template;
        // only need to check length, width, and material
        this.tempTemplate.Components.sort((a, b) => {
          // Compare materials first
          if (a[5] !== b[5]) {
            if (a[5] === innerMaterial) return -1;
            if (b[5] === innerMaterial) return 1;
            return a[5].localeCompare(b[5]);
          }
          
          // If materials are equal, compare lengths
          if (a[3] !== b[3]) {
            return b[3] - a[3];
          }
          
          // If lengths are equal, compare widths
          return b[2] - a[2];
        });
      },
      closeModal() {
        this.isModalVisible = false;
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
