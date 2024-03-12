<!-- App.vue -->
<template>
  <div id="app">
    <ToDoList />
  </div>
</template>

<script>
import ToDoList from './components/ToDoList.vue';

export default {
  components: {
    ToDoList
  }
};
</script>
