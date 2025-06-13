<template>
  <div id="app">
    <router-view/>
  </div>
</template>

<script>
import Clarity from '@microsoft/clarity';
export default {
  mixins: [],
  components: {

  },
  props: {},
  data() {
    return {

    }
  },
  computed: {},
  mounted() {
    Clarity.init('rytq6xvtxh');
  }
}
</script>
<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
html,body,#app{
  width: 100%;
  height: 100%;
  padding: 0;
  margin: 0;
}

.maplibregl-popup{
  max-width: unset!important;
}

nav a {
  font-weight: bold;
  color: #2c3e50;
}

nav a.router-link-exact-active {
  color: #42b983;
}
</styl>
