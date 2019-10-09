<template>
  <div><label><input type="checkbox" :checked="!anyRemaining" @change="allchecked"> Check All</label></div>
</template>

<script>
  export default {
    name: 'todo-all-checked',
    props: {
      anyRemaining: {
        type: Boolean,
        required: true
      }
    },
    methods: {
      allchecked () {
        eventBus.$emit('checkedAllTodo', this.anyRemaining)
      }
    },
  }
</script>

<style >

</style>
