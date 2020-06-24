<template>
  <div id="app" class="container">
    near 2-7
    <TodoList/>
  </div>
</template>

<script>
import TodoList from '@/components/TodoList.vue'
  export default {
    components: {
      TodoList
    }
  }
</script>

<style lang="scss">
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    list-style-type: none; 
  }

  .container {
    max-width: 900px;
    margin: 50px auto;
    font-size: 20px;
  }

</style>
