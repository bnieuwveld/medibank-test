<template>
  <div id="app">
    <Subjects/>
  </div>
</template>

<script>
import Subjects from './components/Subjects.vue'

export default {
  name: 'app',
  components: {
    Subjects,
  }
}
</script>

<style>
#app {
  font-family: Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  padding: 1em;
  color: #2c3e50;
}

Subjects {
  width: 50%;
}
</style>
