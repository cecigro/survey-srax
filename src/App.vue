<template>
  <div id="app">
    <survey-create/>
  </div>
</template>

<script>
import SurveyCreate from './components/SurveyCreate.vue'

export default {
  name: 'App',
  components: {
    SurveyCreate
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
}
</style>
