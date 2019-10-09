<template>
  <div>
    <button :class="{active: filter == 'all'}" @click="cahngeFilter('all')">All</button>
    <button :class="{active: filter == 'active'}" @click="cahngeFilter('active')">Active</button>
    <button :class="{active: filter == 'completed'}" @click="cahngeFilter('completed')">Completed</button>
  </div>
</template>

<script>
  export default {
    name: 'todo-filtered',
    data() {
      return {
        filter: 'all'
      }
    },
    methods: {
      cahngeFilter (filter) {
        this.filter = filter
        eventBus.$emit('filterChanged', this.filter)
      }
    },
  }
</script>

<style>

</style>
