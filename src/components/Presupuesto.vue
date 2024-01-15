<script setup>
  import { ref } from 'vue'
  import Alerta from './Alerta.vue'

  const error = ref('')
  const presupuesto = ref(0)

  const $emit = defineEmits(['definir-presupuesto'])

  const definirPresupuesto = () => {
    if (presupuesto.value <= 0) {
      error.value = 'El presupuesto no es válido'
      setTimeout(() => {
        error.value = ''
      }, 3000)
    }

    $emit('definir-presupuesto', presupuesto.value)
  }
</script>

<template>
  <form @submit.prevent="definirPresupuesto" class="formulario">
    <Alerta v-if="error">{{ error }}</Alerta>
    <div class="campo">
      <label for="presupuesto">Definir Presupuesto</label>
      <input
        type="number"
        id="presupuesto"
        class="presupuesto"
        placeholder="Ejm: 5000"
        :value="presupuesto"
        @input="e => presupuesto = e.target.value"
      >
    </div>
    <input
      type="submit"
      value="Definir Presupuesto"
    >
  </form>
</template>

<style scoped>
  .formulario {
    width: 100%;
  }

  .campo {
    display: grid;
    gap: 2rem;
  }

  .formulario label {
    font-size: 2.3rem;
    color: var(--azul);
    text-align: center;
  }

  .formulario input[type="number"] {
    background-color: var(--gris-claro);
    border-radius: 1rem;
    padding: 1rem;
    border: none;
    font-size: 2.2rem;
    text-align: center;
  }

  .formulario input[type="submit"]{
    background-color: var(--azul);
    border: none;
    padding: 1rem;
    text-align: center;
    font-size: 2rem;
    margin-top: 2rem;
    color: var(--blanco);
    font-weight: 900;
    width: 100%;
    transition: background-color .3s ease-in-out;
  }

  .formulario input[type="submit"]:hover {
    background-color: #1048a4;
    cursor: pointer;
  }
</style>