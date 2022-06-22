<script setup>
  import {ref, computed, provide, watch, onBeforeMount} from 'vue'
  import EditItem from './EditItem.vue';
  import TodoItem from './TodoItem.vue';
  import TodoHeader from './TodoHeader.vue';
  import AlertBox from './AlertBox.vue';
  import TransitionComponent from '../transitions/TransitionComponent.vue'

  const text = ref('')
  const todos = ref([])
  const todosLength = computed(() => todos.value.length)
  const editingTodos = ref([])
  const selectedTodoState = ref('all')
  provide('changeSelectedState', changeSelectedState)
  const filterTodos = ref('')
  const addTodoCheckbox = ref(false)
  const isSortByDate = ref(true)
  const computedTodos = computed(() => {
    let filter = filterBySelectedMode(todos.value)
    if(filterTodos.value < 1) return filter
    else {
      let filterByText = filter.filter(todo => {
        return todo.text.includes(filterTodos.value)
      })
      return filterByText
    }
  })
  const errorMessages = ref({
    addTodo:{
      error: false,
      message: null
    },
    editTodo:{
      error: false,
      message: null
    }
  })

  onBeforeMount(() => {
    const JSONtodos = localStorage.getItem('todos')
    const JSONsorting = localStorage.getItem('sorting')
    const storageTodos = JSON.parse(JSONtodos)
    const storageSorting = JSON.parse(JSONsorting)
    isSortByDate.value = storageSorting ?? JSON.stringify(false)
    todos.value = storageTodos ?? []
  })

  function isTextValid(text){
    if(text.trim().length < 1) {
      return true
    }
    return false
  }

  function handleSubmit(){
    errorMessages.value.addTodo.error = false
    if(isTextValid(text.value)){
      errorMessages.value.addTodo.error = true
      errorMessages.value.addTodo.message = 'To-do must have at least one character'
      return
    }    
    todos.value.push({
      id: Math.floor(Math.random()*100000),
      date: Date.now(),
      text: text.value,
      completed: addTodoCheckbox.value,
      editMode: false
    })
    text.value = ''
    addTodoCheckbox.value = false
  }

  function deleteTodo(id){
    const index = getIndexOfTodo(id)
    todos.value.splice(index,1)
  }

  function toggleTodo(index){
    todos.value[index].editMode = !todos.value[index].editMode
  }

  function getIndexOfTodo(id){
    const todo = computedTodos.value.find(item => item.id === id)
    return todos.value.indexOf(todo)
  }

  function editTodo(id){
    const index = getIndexOfTodo(id)
    toggleTodo(index)
    if(todos.value[index].editMode){
      editingTodos.value.push(Object.assign({index: index}, todos.value[index]))
    }
    else{
      editingTodos.value = editingTodos.value.filter((item) => item.index !== index)
    }
  } 

  function cancelTodo(id){
    const index = getIndexOfTodo(id)
    toggleTodo(index)
    const todoToCancel = editingTodos.value.find((item) => item.index === index)
    todos.value[index].text = todoToCancel.text
  }

  function changeCompleted(id){
    const index = getIndexOfTodo(id)
    todos.value[index].completed = !todos.value[index].completed
  }

  function filterBySelectedMode(todoList){
    if(selectedTodoState.value === 'all') return todoList
    else if(selectedTodoState.value === 'pending'){
      return todoList.filter((todo) => !todo.completed)
    }
    else {
      return todoList.filter((todo) => todo.completed)
    }
  }

  function changeSelectedState(value) { 
    selectedTodoState.value = value 
  }

  function handleSorting(e){
    if(e.target.value === 'false') {
      handleDateSorting()
    }
    else {
      handleNameSorting()
    }
  }

  function handleDateSorting(){
    todos.value.sort((a,b) => {
      return a.date - b.date
    }) 
  }

  function handleNameSorting(){
    todos.value.sort((a,b) => {
      if(a.text > b.text) return 1
      else if(a.text < b.text) return -1
      else return 0
    })
  }

  watch(() => isSortByDate.value, () => {
    localStorage.setItem('sorting', JSON.stringify(isSortByDate.value))
  }, 
  {deep: true})

  watch(todos, ()=> {
    localStorage.setItem('todos', JSON.stringify(todos.value))
  }, {deep: true})

</script>

<template>
  <main>
    <TodoHeader/>
    <form @submit.prevent.enter="handleSubmit">
      <input type="text" class="input" v-model="text" autocomplete="off">
      <button id="submit" class="todoButtons">add</button>
      <label>
        <input type="checkbox" v-model="addTodoCheckbox"/>
        <span>Mark as completed?</span>
      </label>
    </form>  
    <TransitionComponent
      name="fade"
      leaveActive="goUp-leave-active"
      leaveFrom="goUp-leave-from"
      leaveTo="goUp-leave-to"
    >
      <AlertBox v-if="errorMessages.addTodo.error">{{errorMessages.addTodo.message}}</AlertBox>
    </TransitionComponent>
    <h2>Current Length: {{computedTodos.length}} 
        <span v-if="selectedTodoState !== 'all'">| Actual Length: {{todosLength}}</span>
    </h2>
    <div class="selectOptions">
      <select v-model="isSortByDate" @change="(e) => handleSorting(e)">
        <option :value="Boolean(false)" selected>Created</option>
        <option :value="Boolean(true)">Name</option>
      </select>
    </div>
    
    <label>
      Search for a todo:
      <input v-model="filterTodos" class="input"/>
      (displaying {{selectedTodoState}} todos.)  
    </label>
    <TransitionGroup 
      tag="ul" 
      name="fade"
    >
      <div v-for="(todo) in computedTodos" :key="todo" class="allTodos">
        <TransitionComponent 
          name="fade"
        >
            <TodoItem 
              v-if="!todo.editMode"
              :todo="todo" 
              @changeCompleted="changeCompleted(todo.id)"
              @editTodo="editTodo(todo.id)"
              @deleteTodo="deleteTodo(todo.id)"
            />
            <EditItem
              v-else
              :todo="todo"
              v-model:text="todo.text"
              @saveTodo="editTodo(todo.id)"
              @cancelTodo="cancelTodo(todo.id)"
            />
        </TransitionComponent>
      </div>
    </TransitionGroup>
  </main>
</template>


<style>
  .input{
    border: none;
    outline: none;
    background: none;
    border-bottom: 0.125rem solid black;
    font-family: 'Poppins', sans-serif;
    font-size: 1rem;
  }

  form{
    display: flex;
    align-items: center;
    gap: 0.5rem;
    margin: 1rem 0;
  }

  p{
    margin: 0;
  }
  .text{
    display: flex;
    align-items: center;
  }
  #submit{
    border-radius: 0.25rem;
    padding: 0.125rem 0.25rem;
  }
  .todoButtonsContainer{
    display: flex;
    gap: 0.5rem;
  }
  .todoButtons{
    background: none;
    outline: none;
    border-radius: 0.125rem;
    border: none;
    background-color: #131313;
    color: white;
    font-family: 'Poppins', sans-serif;
    transition: 250ms;
  }
  .todoButtons:hover{
    cursor: pointer;
    background: #333;
    transform: translateY(-5%);

  }
  ul{
    list-style: none;
    padding: 0;
    margin: 0;
    position: relative;
  }
  .todoContainer{
    width: 30%;
    display: flex;
    justify-content: space-between;
    padding: 0.125rem 0;
  }

  .selectOptions{
    display: flex;
    margin: 1.5em 0;
    gap: 1.5rem;
  }
  
  .selectOptions > select{
    background: none;
    padding: 0.25rem;
    border-radius: 0.25rem;
    font-family: 'Poppins', sans-serif;
  }

</style>