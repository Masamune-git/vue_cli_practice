<template>
  <div id="app">
      <indexMemo/>
  </div>
</template>

<script>
import indexMemo from './components/indexMemo.vue'
// import Test from './components/test.vue'

export default {
  name:'app',
  components: {    
    indexMemo
  }
}
</script>

<style>
#app {
  width: 100%;
  margin-top: 60px;
}
</style>
