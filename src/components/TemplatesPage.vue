<template>
  <div>
    <div v-for="item in items" :key="item.id" class="button-row">
    <button @click="item.showData = !item.showData">Display</button>
    <button @click="deleteRow(item.id)">Delete</button>
    
    <div v-if="item.showData" class="data-display">
      <p>X: {{ item.x }}</p>
      <p>Y: {{ item.y }}</p>
      <p>Z: {{ item.z }}</p>
    </div>
  </div>

      <!-- Display data modal or tooltip here -->
    </div>
  </template>
  
  <script>
  // import jsonData from './data.json';
  
  export default {
  props: ['items'],
  data() {
    return {
      localItems: this.items
    };
  },
  methods: {
    displayData(item) {
      const localItem = this.localItems.find(i => i.id === item.id);
      if (localItem) {
        localItem.showData = !localItem.showData;
      }
    },
    deleteRow(id) {
      this.localItems = this.localItems.filter(item => item.id !== id);
    }
  }
}
  </script>
  
  <style scoped>
  .button-row {
    display: flex;
    justify-content: space-between;
    margin-bottom: 10px;
  }
  </style>
  