<template lang="pug">
    #app
        link(type='text/css', rel='stylesheet', href='https://www.gstatic.com/firebasejs/ui/3.2.0/firebase-ui-auth.css')
        router-view
</template>

<style lang="scss">
body {
  height: 100vh;
}
#app {
  height: inherit;
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
#nav {
  padding: 30px;
  a {
    font-weight: bold;
    color: #2c3e50;
    &.router-link-exact-active {
      color: #42b983;
    }
  }
}
</style>
