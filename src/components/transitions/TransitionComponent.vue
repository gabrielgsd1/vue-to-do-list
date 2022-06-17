<script setup>
  import { defineProps } from 'vue';
  defineProps({
    name:{
      type: String,
      default: 'fade'
    },
    enterTo:{
      type: String,
      default: 'fade-enter-to'
    },
    enterFrom:{
      type: String,
      default: 'fade-enter-from'
    },
    enterActive:{
      type: String,
      default: 'fade-enter-active'
    },
    leaveActive:{
      type: String,
      default: 'fade-leave-active'
    },
    leaveTo:{
      type: String,
      default: 'fade-leave-to'
    },
    leaveFrom:{
      type: String,
      default: 'fade-leave-from'
    }
  })
  
</script>

<template>
  <Transition 
    :name="name"
    :enter-active-class="enterActive"
    :leave-active-class="leaveActive"
    :enter-to-class="enterTo"
    :leave-to-class="leaveTo"
    :enter-from-class="enterFrom"
    :leave-from-class="leaveFrom"
  >
      <slot></slot>
  </Transition>  
</template>

<style>
  .fade-enter-active, .fade-move{
    transition: all 200ms;
  }
  .fade-leave-active{
    transition: all 125ms;
    width: 100%;
    position: absolute;
  }
  .goUp-leave-active, .goUp-enter-active, .goDown-enter-active, .goDown-leave-active{
    transition: all 125ms;
  }
  .fade-enter-from, .fade-leave-to{
    opacity: 0;
    transform: translateX(-3rem);
  }
  .goUp-leave-to, .goDown-enter-from{
    opacity: 0;
    transform: translateY(-20%);
  }
  .fade-enter-to, .fade-leave-from, .goUp-leave-from{
    opacity: 1;
  }
</style>