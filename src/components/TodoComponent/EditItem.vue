<script setup>
  import { defineEmits, defineProps, ref } from 'vue';
  defineEmits(['saveTodo', 'cancelTodo','update:text'])

  defineProps({
    text: String
  })

  const inputRef = ref(null)
</script>

<template>
  <div class='todoContainer'>
    <input @input="$emit('update:text', inputRef.value)" ref='inputRef' :value="text"  class="input"/>
    <div class="todoButtonsContainer">
      <div><button class="todoButtons" @click="$emit('saveTodo', text)">save</button></div>
      <div><button class="todoButtons" @click="$emit('cancelTodo')">cancel</button></div>
    </div>
  </div>
</template>

<style>
</style>