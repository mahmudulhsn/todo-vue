<template>
<div>
  <input type="text" class="todo-input" placeholder="What needs to be done?" v-model="newTodo" @keyup.enter="addTOdo">
  <div v-for="(todo, index) in todos" :key="todo.id" class="todo-item">
    <div class="todo-item-left">
      <div v-if="!todo.editing" @dblclick="editTodo(todo)" class="todo-item-label">{{ todo.title }}</div>
      <input v-else type="text" class="todo-item-edit" v-model="todo.title" @blur="doneEdit(todo)" @keyup.enter="doneEdit(todo)" @keyup.esc="cancelEdit(todo)" v-focus>
    </div>
    <div class="remove-item">
      <span class="remove" @click="removeTodo(index)">
        &times;
      </span>
    </div>
  </div>
</div>
</template>

<script>
export default {
  name: 'todo-list',
  data () {
    return {
      newTodo: '',
      idForTodo:3,
      beforeEditCache: '',
      todos:[
        {
          'id': 1,
          'title': 'Finish Vue Screencast',
          'completed': false,
          'editing': false
        },
        {
          'id': 2,
          'title': 'Take Oder world',
          'completed': false,
          'editing': false
        }
      ]
    }
  },
  directives: {
    focus: {
      inserted: function (el) {
        el.focus()
      }
    }
  },
  methods: {
    addTOdo() {
      if (this.newTodo.trim().length == 0) {
        return
      }
      this.todos.push({
        id: this.idForTodo,
        title: this.newTodo,
        completed: false
      })

      this.newTodo = ' ',
      this.idForTodo++
    },
    removeTodo(index) {
      this.todos.splice(index, 1);
    },
    editTodo(todo) {
      this.beforeEditCache = todo.title
      todo.editing = true
    },
    doneEdit(todo) {
      todo.editing = false
    },
    cancelEdit(todo) {
      todo.title = this.beforeEditCache
      todo.editing = false
    }
  },
}
</script>

<style>
.todo-input{
  width: 100%;
  padding: 10px 18px;
  font-size: 18px;
  margin-bottom: 16px;
}
.todo-item{
  margin-bottom: 12px;
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.remove-item{
  cursor: pointer;
  margin-left: 14px;
}
.remove{
  color: black;
}
.remove:hover{
  color: red;
}

.todo-item-left {
    display: flex;
    align-items: center;
}
.todo-item-label {
  padding: 10px;
  border: 1px solid white;
  margin-left: 12px;
}
.todo-item-edit {
  font-size: 24px;
  color: #2c3e50;
  margin-left: 12px;
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc; //override defaults
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  &:focus {
    outline: none;
  }
}

</style>
