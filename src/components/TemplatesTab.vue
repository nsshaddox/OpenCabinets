<template>
  <div class="grid-container">
    <div class="row-containers" v-for="(template, index) in templates" :key="template.Name">
      <CButtonGroup class="button-set" size="sm" role="group" aria-label="Button group with nested dropdown">
        <CButton @click="toggleTable(index)" class="toggle-table" :color="buttonColor">{{ getButtonText(index) }}</CButton>
        <CDropdown  variant="btn-group">
          <CDropdownToggle class="dropdown" :color="buttonColor"></CDropdownToggle>
          <CDropdownMenu>
            <CDropdownItem href="#">Action</CDropdownItem>
            <CDropdownItem href="#">Another action</CDropdownItem>
            <CDropdownItem href="#">Something else here</CDropdownItem>
            <CDropdownDivider/>
            <CDropdownItem href="#">Separated link</CDropdownItem>
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
            <td v-for="cell in row" :key="cell">{{ cell }}</td>
          </template>
        </tr>
      </table>
    </div>
  </div>
</template>

<script>
  import {  CDropdown, CDropdownToggle, CDropdownItem, CDropdownMenu, 
            CDropdownDivider, CButton,CButtonGroup } from '@coreui/vue';

  export default {
    props: {
      templates: {
        type: Array,
        default: () => [],
      },
    },
    name: "TemplatesTab",
    data() {
      return {
        visibleTables: new Array(this.templates.length).fill(true),
        buttonColor: "info"
      }
    },
    components: {
      CDropdown, CDropdownToggle, CDropdownItem, CDropdownMenu,
      CDropdownDivider, CButton,CButtonGroup,
    },
    methods: {
      toggleTable(index) {
        this.visibleTables[index] = !this.visibleTables[index];
        console.log(this.visibleTables[index]);
      },
      removeRow(index) {
        this.$emit('remove-template-t', index);
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
      }
    },
  }
</script>

<style scoped>
.grid-container {
  max-width: 1200px;
  min-width: 750px;
  margin: 10px;
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

.dropdown {
  height: 25px;
  display: flex;
  align-items: center;
  color: white;
  margin-left: 3px;
  /* border: 1px solid black; */
  border-radius: 0px;
/* stuff in here */
}



/* .show-cabinet {
  flex-grow: 1;
  height: 20px;
  padding: 0 0;
  padding-left: 8px;
  background-color: #4a68cb;;
  color: #ffffff;
  border: 1px solid black;
  border-radius: 3px;
  cursor: pointer;
  text-align: center;
  transition: background-color 0.3s ease-in-out;
  display: flex;
  justify-content: left;
  align-items: center;
} */

/* .delete-cabinet,
.add-cabinet{
  height: 20px !important;
  width: 40px;
  padding: 0 0;
  background-color: #4a68cb;
  color: #ffffff;
  border: 1px solid black;
  border-radius: 3px;
  cursor: pointer;
  text-align: center;
  transition: background-color 0.2s ease-in-out;
  display: flex;
  justify-content: center;
  align-items: center;
} */

/* .delete-cabinet:hover,
.add-cabinet:hover,
.show-cabinet:hover {
  background-color: #a4b6f2;;
} */



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
