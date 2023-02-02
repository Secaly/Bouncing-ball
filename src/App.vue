<template>
  <Ball />
</template>

<script lang="ts">
import { Options, Vue } from 'vue-class-component';
import Ball from './components/BallBounce.vue';

@Options({
  components: {
    Ball,
  },
})
export default class App extends Vue {}
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
body {
  overflow: hidden;
  margin: 0px;
}
</style>
