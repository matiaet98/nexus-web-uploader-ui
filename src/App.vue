<template>
  <div id="app">
    <Home/>
    <br>
    <b-container>
      <b-row>
        <b-col md="12">
          <SeccionFormulario/>
        </b-col>
    </b-row>
    </b-container>
  </div>
</template>

<style lang="postcss">
  body{
    background:rgba(0, 0, 0, 0.1);
  }
  
</style>
>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import Home from './components/Home.vue';
import SeccionFormulario from './components/SeccionFormulario.vue';

@Component({
  components: {
    Home,
    SeccionFormulario,
  },
})
export default class App extends Vue {}
</script>