<script setup>
import TodoList from './components/TodoList.vue'
</script>

<template>
  <div class="container">
    <img alt="Vue logo" class="logo" src="./assets/logo.svg" width="125" height="125" />
    <todo-list/>
  </div>
</template>

<style scoped>
* {
  box-sizing: border-box;
}

.container {
  max-width: 600px;
  margin: 0 auto;
}

.logo {
  display: block;
  margin: 75px;
}
</style>
