<template>
  <div>
    <h1>TODO</h1>

<p>{{ completedTodos }}</p>

    <TodoForm 
    @onNewTodo="handleNewTodo"
    @onUpdateTodo="onUpdateTodo"
    />
    <div style="margin-top: 20px;">
      <TodoListItem 
      v-for="todo in todoList " 
      :key="todo.id" 
      :todo="todo"
      @onToggleisCheked="handleToggleIsChecked"
      @onToggleEditTodo="handleToggleEditTodo"
      @onUpdateTodo="handleUpdateTodo"
      @onRemoveTodo="handleRemoveTodo"
      />
    </div>
  </div>
</template>

<script>
import TodoForm from './components/TodoForm/TodoForm.vue';
import TodoListItem from './components/TodoListItem/TodoListItem.vue';
export default {
  name: 'App',
  components: {
    TodoForm,
    TodoListItem
  },
  data() {
    return {
      todoList: [],
    }
  },
  computed: {
    completedTodos() {
      return this.todoList.filter(todo => todo.isChecked).length
    }
  },

methods: {
  handleNewTodo(newTodo) {
    this.todoList.push(newTodo);
    // console.log(this.todoList);
    },
    handleToggleIsChecked({value, id}) {
      const todo = this.findTodo(id);
      todo.isChecked = value;
      console.log(this.todoList);
    },
    findTodo(id) {
      return this.todoList.find(todo => todo.id === id);
    },
    handleToggleEditTodo(id) {
      const todo = this.findTodo(id);
      todo.editMode = !todo.editMode
      // console.log(id);
    },
    handleUpdateTodo(updateTodo) {
      this.todoList = this.todoList.map((todo) => { 
  if (todo.id === updateTodo.id) {
    return updateTodo
  }
  return todo
})
    },
    handleRemoveTodo(id) {
      this.todoList = this.todoList.filter(todo => todo.id !== id);
    }
  }
}
</script>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
