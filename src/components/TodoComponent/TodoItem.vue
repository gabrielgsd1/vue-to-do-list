<script setup>
  import { defineProps, defineEmits } from 'vue';

  const props = defineProps({
    todo: Object
  })
  defineEmits(['changeCompleted', 'editTodo', 'deleteTodo'])

</script>

<template>
    <div class="todoContainer">
      <div class="text" :class="{done: props.todo.completed}">
        <label>
          <input type="checkbox" @change="$emit('changeCompleted')" :checked="todo.completed"/>
          <span>{{props.todo.text}}</span>
        </label>
      </div>  
      <div class="todoButtonsContainer">
        <div><button class="todoButtons" @click="$emit('editTodo')">edit</button></div>
        <div><button class="todoButtons" @click="$emit('deleteTodo')">delete</button></div>
      </div>
    </div>
</template>


<style>
  .done{
    color: green;
    font-style: italic;
    text-decoration: dashed;
  }
</style>