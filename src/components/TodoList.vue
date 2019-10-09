<template>
<div>
  <input type="text" class="todo-input" placeholder="What needs to be done?" v-model="newTodo" @keyup.enter="addTOdo">

  <transition-group enter-active-class="animated fadeInUp" leave-active-class="animated fadeOutDown">
  <todo-item v-for="(todo, index) in todosFiltered" :key="todo.id" :todo="todo" :index="index"  :checkAll="!anyRemaining">
  </todo-item>
  </transition-group>

  <div class="extra-container">
    <todo-all-checked :anyRemaining="anyRemaining"></todo-all-checked>
    <todo-items-remaining></todo-items-remaining>
  </div>
  <div class="extra-container">
    <todo-filtered></todo-filtered>
    <div>
      <transition name="fade">
        <todo-clear-completed :showClearCompletedButton="showClearCompletedButton"></todo-clear-completed>
      </transition>
    </div>
  </div>
</div>
</template>

<script>
import TodoItem from './TodoItem'
import TodoItemsRemaining from './TodoItemsRemaining'
import TodoAllChecked from './TodoAllChecked'
import TodoFiltered from './TodoFiltered'
import TodoClearCompleted from './TodoClearCompleted'
export default {
  name: 'todo-list',
  components:{
    TodoItem,
    TodoItemsRemaining,
    TodoAllChecked,
    TodoFiltered,
    TodoClearCompleted
  },
  data () {
    return {
      newTodo: '',
      idForTodo:3,
      beforeEditCache: '',
      filter: 'all',
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
  created () {
    eventBus.$on('removedTodo', (index) => this.removeTodo(index))
    eventBus.$on('finishEdit', (data) => this.finishEdit(data))
    eventBus.$on('checkedAllTodo', (checked) => this.checkAllTodos(checked))
    eventBus.$on('filterChanged', (filter) => this.$store.state.filter = filter)
    eventBus.$on('clearCompletedTodos', () => this.clearCompleted())
  },
  beforeDestroy() {
    eventBus.$off('removedTodo', (index) => this.removeTodo(index))
    eventBus.$off('finishEdit', (data) => this.finishEdit(data))
    eventBus.$off('checkedAllTodo', (checked) => this.checkAllTodos(checked))
    eventBus.$off('filterChanged', (filter) => this.$store.state.filter = filter)
    eventBus.$off('clearCompletedTodos', () => this.clearCompleted())
  },
  computed: {
    remaining() {
      return this.$store.getters.remaining
    },
    anyRemaining() {
      return this.$store.getters.anyRemaining
    },
    todosFiltered() {
      return this.$store.getters.todosFiltered
    },
    showClearCompletedButton() {
      return this.$store.getters.showClearCompletedButton
    }
  },
  methods: {
    addTOdo() {
      if (this.newTodo.trim().length == 0) {
        return
      }
      this.$store.state.todos.push({
        id: this.idForTodo,
        title: this.newTodo,
        completed: false,
        editing: false
      })

      this.newTodo = '',
      this.idForTodo++
    },
    removeTodo(index) {
      this.$store.state.todos.splice(index, 1);
    },
    cancelEdit(todo) {
      todo.title = this.beforeEditCache
      todo.editing = false
    },
    checkAllTodos() {
      this.$store.state.todos.forEach((todo) => todo.completed = event.target.checked)
      // this.$store.state.todos.forEach((todo, index) => {
      //   todo.completed = event.target.checked
      //   })
    },
    clearCompleted() {
      this.$store.state.todos = this.$store.state.todos.filter(todo => !todo.completed)
    },
    finishEdit(data) {
      this.$store.state.todos.splice(data.index, 1, data.todo)
    }
  },
}
</script>

<style>
/* animate css cdn */
@import url("https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.7.2/animate.min.css");
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
  animation-duration: .3s;
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
  border: 1px solid #ccc;
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
}

.completed{
  text-decoration: line-through;
  color: grey;
}

.extra-container {
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 16px;
    border-top: 1px solid lightgrey;
    padding-top: 14px;
    margin-bottom: 14px;
  }
  button {
    font-size: 14px;
    background-color: white;
    appearance: none;
    &:hover {
      background: lightgreen;
    }
    &:focus {
      outline: none;
    }
  }
  .active {
    background: lightgreen;
  }

  /* transition */
  .fade-enter-active, .fade-leave-active{
    transition: opacity .5s;
  }
  .fade-enter, .fade-leave-to{
    opacity: 0;
  }
</style>
