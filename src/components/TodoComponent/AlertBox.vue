<template>
  <div>
    <slot id="warning"></slot>
  </div>
</template>

<style scoped>
  div{
    margin-top: 0.25rem;
    background: #BB0000;
    display: inline-block;
    color: white;
    font-style: bold;
    padding: 0.5rem;
    width: fit-content;
    border-radius: 0.5rem;
  }
</style>