<template>
<b-card header="Nexus Uploader" header-bg-variant="dark" header-text-variant="white">
  <Formulario/>
</b-card>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import Formulario from './Formulario.vue';

@Component({
  components: {
    Formulario,
  },
})
export default class SeccionFormulario extends Vue {}
</script>


