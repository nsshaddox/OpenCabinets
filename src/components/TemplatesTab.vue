<template>
  <div class="grid-container">
    <div class="row-containers" v-for="(template, index) in templates" :key="template.Name">
      <div class="button-set">
        <button @click="addTemplate(index)" class="add-cabinet">add</button>
        <button @click="toggleTable(index)" class="show-cabinet"><b style="margin-left: 5px;">{{ getButtonText(index) }}</b></button>
        <button @click="removeRow(index)" class="delete-cabinet">delete</button>
      </div>
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
// import standardTemplates  from './templates/standardTemplates.json';
// import projectTemplates   from './templates/projectTemplates.json'

  export default {
    props: {
      templates: {
        type: Array,
        default: () => []
      },
    },
    name: "TemplatesTab",
    data() {
      return {
        visibleTables: new Array(this.templates.length).fill(true)
      }
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
  margin: 20px auto;
  /* width: 100%; */
}

.button-set {
  display: flex; /* Aligns buttons horizontally */
  justify-content: space-between; /* Space buttons evenly */
  margin-bottom: 0px;
}


.show-cabinet {
  height: 20px;
  padding: 0 0;
  background-color: #1ab6ac;
  color: #ffffff;
  border: 1px solid black;
  border-radius: 3px;
  cursor: pointer;
  text-align: center;
  transition: background-color 0.2s ease-in-out;
  display: flex;
  justify-content: left;
  align-items: center;
}

.delete-cabinet,
.add-cabinet{
  height: 20px;
  padding: 0 0;
  background-color: #1ab6ac;
  color: #ffffff;
  border: 1px solid black;
  border-radius: 3px;
  cursor: pointer;
  text-align: center;
  transition: background-color 0.2s ease-in-out;
  display: flex;
  justify-content: center;
  align-items: center;
}

.show-cabinet {
  flex-grow: 1;
  min-width: 0;
  margin-right: 3px;
  margin-left: 3px;
  text-align: right;
}

.add-cabinet {
  aspect-ratio: 1;
  width: 30px;
  background-color: rgb(175, 30, 30);
}
.delete-cabinet {
  aspect-ratio: 1;
  width: 45px;
  background-color: rgb(175, 30, 30);
}

.delete-cabinet:hover {
  background-color: rgb(127, 22, 22);
}
.show-cabinet:hover {
  background-color: #189b93;
}

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
td:nth-child(1),
th:nth-child(3),
td:nth-child(3),
th:nth-child(4),
td:nth-child(4),
th:nth-child(5),
td:nth-child(5) {
  width: 54px;
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
