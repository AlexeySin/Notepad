<template>
  <div id="app">
    <ToDo/>
  </div>
</template>

<script>
import ToDo from './components/ToDoComponent'

window.$ = require('jquery')
window.JQuery = require('jquery')

export default {
  name: 'App',
  components: {
    ToDo
  }
}
</script>

<style scoped>
</style>
