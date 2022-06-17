<script setup>
  import {inject} from 'vue'
  const changeSelectedState = inject('changeSelectedState')
</script>

<template>
  <section>
    <label>
      <input type="radio" id='all' name='todosState' checked @change="changeSelectedState('all')"/>
      All todos
    </label>
    <label>
      <input type="radio" id='notDone' name='todosState' @change="changeSelectedState('pending')"/>
      Pending Todos
    </label>
    <label>
      <input type="radio" id='done' name='todosState' @change="changeSelectedState('finished')"/>
      Finished Todos
    </label>
  </section>
</template>

<style scoped>
  section{
    display: flex;
    gap: 1rem;
    box-sizing: border-box;
    margin: 1rem 0;
  }
</style>