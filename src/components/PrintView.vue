<template>
  <div>
    <!-- 
      This page will display the templates similar to that of the
      project page and templates page but without tabs as a modal. 
      It also needs to be formatted such that the page doesn't split
      in the middle of cabinet parts.
     -->
  </div>
</template>

<script>
  export default {
    props: {
      templates: {
        type: Array,
        default: () => []
      },
    },
  }
</script>

<style scoped>

</style>