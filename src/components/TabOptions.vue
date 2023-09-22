<template>
  <div class="tab" @click="selectTab">
    {{ title }}
  </div>
</template>

<script>
export default {
  props: ['title', 'isSelected'],
  methods: {
    selectTab() {
      this.$emit('select', this.title);
    }
  }
}
</script>

<style scoped>
.tab {
  display: inline-block;
  padding: 10px 20px;
  margin-right: 5px;
  border: 1px solid #ccc;
  border-radius: 5px 5px 0 0;
  background-color: #f5f5f5;
  cursor: pointer;
}
</style>
