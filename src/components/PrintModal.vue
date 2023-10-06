<template>
  <CButton class="button" color="primary" @click="closePrint">back</CButton>
  <CButton class="button" color="primary" @click="print">print</CButton>
  <!-- Format the templates so they are printable -->
  <div class="grid-container">
    <div class="row-containers" v-for="(template, index) in printData.printableTemplates" :key="template.Name">
      <!-- <CButton class="toggle-table"> -->
        <span class="toggle-table">{{ getButtonText(index) }}</span>
      <!-- </CButton> -->
      <table class="table">
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
      CButton,

    } from '@coreui/vue';

  export default {
    name: "PrintModal",
    props: {
      printData: {
        type: Object,
        default: () => ({})
      },
    },
    data() {
      return { 
        isLoaded: false,
      }
    },
    emits: ['close-print'],
    components: {
      CButton,
    },
    methods: {
      print() {
        window.print()
      },
      closePrint() {
        this.$emit('close-print')
      },

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
       * Generates button text based on template metadata.
       * @param {number} index - The index of the template.
       * @returns {string} The generated button text.
       */
      getButtonText(index) {
        const data = this.printData.printableTemplates[index].MetaData;
        const spacer = ' || ';
        let result = '';

        result += data.Quantity + spacer;
        result += this.printData.printableTemplates[index].Name + spacer;
        result += data.Style + spacer;
        result += `W: ${data.Width}` + spacer;
        result += `D: ${data.Depth}` + spacer;
        result += `H: ${data.Height}` + spacer;
        result += `Inner: ${data.InnerMaterial}` + spacer;
        result += `Outer: ${data.OuterMaterial}`;

        return result;
      },
    },
  }
</script>

<style scoped>
@media print {
  .button{
    display:none;
  }
  .table {
    font-size: small;
  }
  .toggle-table {
    font-size: small;
    padding-left: 10px;
  }
}

.grid-container {
  max-width: 1200px;
  min-width: 750px;
}

.button-set {
  display: flex;
  margin-bottom: 2px;
  margin-top: 2px;
}

.toggle-table {
  height: 25px;
  display: flex;
  align-items: center;
  font-weight: bold;
  color: rgb(0, 0, 0);
  border: 2px solid rgb(0, 0, 0);
  border-radius: 0px;
  padding-left: 10px;
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
