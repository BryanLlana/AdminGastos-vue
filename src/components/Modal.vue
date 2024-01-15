<script setup>
  import iconoCerrarModal from '../assets/img/cerrar.svg'
  import Alerta from './alerta.vue'
  import { ref } from 'vue'

  const props = defineProps({
    modal: {
      type: Object,
      required: true
    },
    disponible: {
      type: Number,
      required: true
    },
    nombre: {
      type: String,
      required: true
    },
    categoria: {
      type: String,
      required: true
    },
    fecha: {
      type: String,
      required: true
    },
    cantidad: {
      type: Number,
      required: true
    },
    id: {
      type: String || null,
      required: true
    }
  })

  const $emit = defineEmits(['cerrar-modal', 'crear-gasto', 'eliminar-gasto', 'update:nombre', 'update:categoria', 'update:cantidad'])

  const error = ref('')
  const old = props.cantidad

  const validarGasto = () => {
    if ([props.nombre, props.categoria, props.cantidad].includes('')) {
      error.value = 'Todos los campos son obligatorios'
      setTimeout(() => {
        error.value = ''
      }, 3000)
      return
    }

    if (props.id) {
      if (Number(props.cantidad) > Number(old) + Number(props.disponible)){
        error.value = 'No tienes saldo suficiente'
        setTimeout(() => {
          error.value = ''
        }, 3000)
        return
      }
    } else {
      if (Number(props.cantidad) > Number(props.disponible)) {
        error.value = 'No tienes saldo suficiente'
        setTimeout(() => {
          error.value = ''
        }, 3000)
        return
      }
    }

    $emit('crear-gasto', props.id)
  }
</script>

<template>
  <div class="modal">
    <div class="cerrar-modal">
      <img 
        :src="iconoCerrarModal"
        @click="$emit('cerrar-modal')"
      />
    </div>
    <div class="contenedor contenedor-formulario" :class="[modal.animar ? 'animar' : 'cerrar']">
      <form @submit.prevent="validarGasto" class="formulario">
        <legend>{{ id ? 'Editar Gasto' : 'Añadir Gasto' }}</legend>
        <Alerta v-if="error">{{ error }}</Alerta>
        <div class="campo">
          <label for="nombre">Nombre Gasto:</label>
          <input
            type="text"
            id="nombre"
            placeholder="Ejm: Visita museo"
            :value="nombre"
            @input="$emit('update:nombre', $event.target.value)"
          >
        </div>
        <div class="campo">
          <label for="cantidad">Cantidad:</label>
          <input
            type="number"
            id="cantidad"
            placeholder="Ejm: 500"
            :value="cantidad"
            @input="$emit('update:cantidad', $event.target.value)"
          >
        </div>
        <div class="campo">
          <label for="categoria">Categoría:</label>
          <select
            id="categoria"
            :value="categoria"
            @change="$emit('update:categoria', $event.target.value)"
          >
            <option value="">--Seleccione--</option>
            <option value="ahorro">Ahorro</option>
            <option value="comida">Comida</option>
            <option value="casa">Casa</option>
            <option value="gastos">Gastos Varios</option>
            <option value="ocio">Ocio</option>
            <option value="salud">Salud</option>
            <option value="suscripciones">Suscripciones</option>
          </select>
        </div>

        <input
          type="submit"
          :value="[id ? 'Guardar Cambios' : 'Enviar Gasto']"
        >
      </form>

      <button @click="$emit('eliminar-gasto', id)" v-if="id" type="button" class="btn-eliminar">
        Eliminar Gasto
      </button>
    </div>
  </div>
</template>

<style scoped>
  .modal {
    position: absolute;
    background-color: rgb(0 0 0 / 0.9);
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
  }

  .cerrar-modal {
    position: absolute;
    right: 3rem;
    top: 3rem;
  }
  
  .cerrar-modal img {
    width: 3rem;
    cursor: pointer;
  }
  .contenedor-formulario {
    transition-property: all;
    transition-duration: 300ms;
    transition-timing-function: ease-in;
    opacity: 1;
  }

  .contenedor-formulario.animar {
    opacity: 1;
  }

  .contenedor-formulario.cerrar {
    opacity: 0;
  }

  .formulario {
    margin: 10rem auto 0 auto;
    display: grid;
    gap: 2rem;
  }
  .formulario legend {
    text-align: center;
    color: var(--blanco);
    font-size: 3rem;
    font-weight: 700;
  }

  .campo {
    display: grid;
    gap: 2rem;
  }

  .formulario input, .formulario select {
    background-color: var(--gris-claro);
    border-radius: 1rem;
    padding: 1rem;
    border: none;
    font-size: 2.2rem;
  }

  .formulario label {
    color: var(--blanco);
    font-size: 3rem;
  }

  .formulario input[type=submit] {
    background-color: var(--azul);
    color: var(--blanco);
    font-weight: 700;
    cursor: pointer;
  }

  .btn-eliminar {
    border: none;
    padding: 1rem;
    width: 100%;
    background-color: #ef4444;
    font-weight: 700;
    font-size: 1.2rem;
    color: var(--blanco);
    margin-top: 3rem;
    cursor: pointer;
  }
</style>