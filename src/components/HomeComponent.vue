<script setup>
import HeaderComponent from "./Header/HeaderComponent.vue";
import TodoComponent from "./TodoComponent/TodoComponent.vue";
</script>

<template>
  <div class="container">
    <HeaderComponent/>
    <div class="content">
      <TodoComponent/>
    </div>
  </div>
</template>

<style scoped>
  .container{
    display: flex;
    flex-direction: column;
    align-items: center;
    width: 100%;
  }
  .content{
    box-sizing: border-box;
    padding: 2rem;
    width: 90%;
    background: #eaeaea;
  }
</style>