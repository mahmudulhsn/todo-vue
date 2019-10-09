<template>
  <div class="todo-item">
    <div class="todo-item-left">
      <input type="checkbox" v-model="completed" @change="doneEdit">
      <div v-if="!editing" @dblclick="editTodo" class="todo-item-label" :class="{ completed : completed }">{{ title }}</div>
      <input v-else type="text" class="todo-item-edit" v-model="title" @blur="doneEdit" @keyup.enter="doneEdit" @keyup.esc="cancelEdit" v-focus>
    </div>
    <div class="remove-item">
      <button @click="pluralize">Plural</button>
      <span class="remove" @click="removeTodo(index)">
        &times;
      </span>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'todo-item',
    props:{
      todo:{
        type: Object,
        required: true
      },
      index:{
        type: Number,
        required: true
      },
      checkAll: {
        type: Boolean,
        required: true
      }
    },
    data() {
      return {
        'id': this.todo.id,
        'title': this.todo.title,
        'completed': this.todo.completed,
        'editing': this.todo.editing,
        'beforeEditCache': ''
      }
    },
    created() {
      eventBus.$on('pluralizzze', this.handlePlural)
    },
    beforeDestroy() {
      eventBus.$off('pluralizzze', this.handlePlural)
    },
    watch: {
      checkAll() {
        //  if (this.checkAll) {
        //    this.completed = true
        //  } else {
        //    this.completed = this.todo.completed
        //  }
        this.completed = this.checkAll ? true : this.todo.completed
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
      removeTodo(index) {
        eventBus.$emit('removedTodo', index)
      },
      editTodo() {
        this.beforeEditCache = this.title
        this.editing = true
      },
      doneEdit() {
        if (this.title.trim() == '') {
          this.title = this.beforeEditCache
        }
        this.editing = false
        eventBus.$emit('finishEdit', {
          'index': this.index,
          'todo': {
            'id': this.id,
            'title': this.title,
            'completed': this.completed,
            'editing': this.editing,
          }
        })
      },
      cancelEdit() {
        this.title = this.beforeEditCache
        this.editing = false
      },
      pluralize () {
        eventBus.$emit('pluralizzze')
      },
      handlePlural () {
        this.title = this.title + 's'
        eventBus.$emit('finishEdit', {
          'index': this.index,
          'todo': {
            'id': this.id,
            'title': this.title,
            'completed': this.completed,
            'editing': this.editing,
          }
        })
      }
    },

  }
</script>

<style scoped>

</style>
