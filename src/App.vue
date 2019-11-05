<template>
  <div id="app">
    <Elevator></Elevator>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import HelloWorld from './components/HelloWorld.vue';
import Elevator from './views/Elevator.vue';


@Component({
  components: {
    Elevator,
  },
})
export default class App extends Vue {}
</script>

<style lang="scss">

#app {
font-family: 'Tajawal', sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;

}
</style>
