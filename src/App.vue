<template>
  <div id="app">
    <CurrencyConverter msg='Currency Converter'/>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import CurrencyConverter from './components/CurrencyConverter.vue';

@Component({
  components: {
    CurrencyConverter,
  },
})
export default class App extends Vue {}
</script>

<style lang="scss">
$base-color: #444;
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: $base-color;
  margin-top: 60px;
}
</style>
