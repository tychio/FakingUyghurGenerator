<template>
  <div id="app">
    <header>
      <img class="logo" width="50" src="./assets/logo.png">
    </header>
    <Painter></Painter>
  </div>
</template>

<script>
import Painter from './components/Painter'

export default {
  name: 'app',
  components: {
    Painter
  }
}
</script>

<style>
html, body {
  margin: 0;
  padding: 0;
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

header {
  overflow: hidden;
  height: 70px;
  background: #eee;
}

.logo {
  float: left;
  margin: 10px;
  width: 50px;
}
</style>
