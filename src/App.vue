<template>
  <div>
    <div class="tabs">
      <Tab
        v-for="tab in tabs"
        :key="tab.title"
        :title="tab.title"
        @select="selectTab"
      />
    </div>
    
    <!-- Tab Contents -->
    <div v-if="selectedTab === 'Tab 1'" class="tab-content">
      <!-- Content for Tab 1 -->
    </div>
    
    <div v-if="selectedTab === 'Tab 2'" class="tab-content">
      <TemplatesPage :items="items"/>
    </div>

    
    <div v-if="selectedTab === 'Tab 3'" class="tab-content">
      <!-- Content for Tab 3 -->
    </div>
  </div>
</template>

<script>
import Tab from './components/TabOptions.vue';
import TemplatesPage from './components/TemplatesPage.vue';
import jsonData from './templates/standard/data.json';

export default {
  components: {
    Tab,
    TemplatesPage
    // If you want to use TemplatesTab as a separate component, import and register it here too.
  },
  data() {
    return {
      tabs: [
        { title: 'Tab 1' },
        { title: 'Tab 2' },
        { title: 'Tab 3' }
      ],
      selectedTab: 'Tab 1',
      items: jsonData.map(item => ({ ...item, showData: false }))
    };
  },

  methods: {
    selectTab(title) {
      this.selectedTab = title;
    },
    displayData(/*item*/) {
      // Logic to display the JSON object data
    },
    deleteRow(id) {
      this.items = this.items.filter(item => item.id !== id);
    }
  }
}
</script>

<style>
.tabs {
  display: flex;
  border-bottom: 1px solid #ccc;
}
.tab-content {
  padding: 20px;
}

.data-display {
  margin-top: 10px;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  background-color: #f5f5f5;
}

</style>
