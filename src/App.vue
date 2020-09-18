<template>
  <img class="logo" alt="ATMOSTECH logo" src="./assets/logo.png">  
  <Page/>
</template>

<script>
import Page from './components/page.vue'

export default {
  name: 'App',
  components: {
    Page,
    
  }
}
</script>

<style scoped>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
.logo {
  display: flex;
  background-color: #fdfdfd;
  width: 300px;
  margin: auto;
}

</style>
