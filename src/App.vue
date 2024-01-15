<script setup>
  import ControlPresupuesto from './components/ControlPresupuesto.vue'
  import Presupuesto from './components/Presupuesto.vue'
  import iconoNuevoGasto from './assets/img/nuevo-gasto.svg'
  import Modal from './components/Modal.vue'
  import Gasto from './components/Gasto.vue'
  import Filtro from './components/Filtro.vue'
  import { generarId } from './helpers'

  import { ref, reactive, onMounted, watch, computed } from 'vue'

  const presupuesto = ref(0)
  const disponible = ref(0)
  const gastado = ref(0)
  const gastos = ref([])
  const modal = reactive({
    mostrar: false,
    animar: false
  })
  const filtro = ref('')
  
  const gasto = reactive({
    id: null,
    categoria: '',
    nombre: '',
    fecha: '',
    cantidad: 0,
  })

  watch([gastos, presupuesto], () => {
    localStorage.setItem('gastos', JSON.stringify(gastos.value))
    localStorage.setItem('presupuesto', presupuesto.value)
    localStorage.setItem('disponible', disponible.value)
    localStorage.setItem('gastado', gastado.value)
  }, {
    deep: true
  })

  onMounted(() => {
    const presupuestoLS = localStorage.getItem('presupuesto')
    const disponibleLS = localStorage.getItem('disponible')
    const gastadoLS = localStorage.getItem('gastado')
    const gastosLS = localStorage.getItem('gastos')

    if (presupuestoLS && disponibleLS && gastadoLS && gastosLS) {
      presupuesto.value = Number(presupuestoLS)
      disponible.value = Number(disponibleLS)
      gastado.value = Number(gastadoLS)
      gastos.value = JSON.parse(gastosLS)
    }
  })

  const resetearApp = () => {
    if (confirm('¿Seguro que desea reiniciar el app?')) {
      presupuesto.value = 0
      disponible.value = 0
      gastado.value = 0
      gastos.value = []
    }
  }

  const definirPresupuesto = valor => {
    presupuesto.value = valor
    disponible.value = valor
  }

  const mostrarModal = () => {
    modal.mostrar = true
    setTimeout(() => {
      modal.animar = true
    }, 300)
  }

  const cerrarModal = () => {
    modal.animar = false
    setTimeout(() => {
      modal.mostrar = false
    }, 300)

    //* Reiniciar gasto
    Object.assign(gasto, {
      id: null,
      nombre: '',
      categoria: '',
      fecha: '',
      cantidad: 0
    })
  }

  const crearGasto = id => {
    if(id) {
      const index = gastos.value.findIndex(gastoState => gastoState.id === id)
      gastos.value[index] = { ...gasto }
    } else {
      gasto.id = generarId()
      gasto.fecha = Date.now()
      gastos.value.push({ ...gasto })
    }

    gastado.value = gastos.value.reduce((total, gastoState) => total + Number(gastoState.cantidad), 0)
    disponible.value = presupuesto.value - gastado.value

    //* Reiniciar gasto
    Object.assign(gasto, {
      id: null,
      nombre: '',
      categoria: '',
      fecha: '',
      cantidad: 0
    })

    cerrarModal()
  }

  const editarGasto = id => {
    mostrarModal()
    const gastoSeleccionado = gastos.value.filter(gastoState => gastoState.id === id)[0]
    Object.assign(gasto, gastoSeleccionado)
  }

  const eliminarGasto = id => {
    gastos.value = gastos.value.filter(gastoState => gastoState.id !== id)
    gastado.value = gastos.value.reduce((total, gastoState) => total + Number(gastoState.cantidad), 0)
    disponible.value = presupuesto.value - gastado.value
    cerrarModal()
  }

  const filtroGastos = computed(() => {
    if (filtro.value === '') {
      return gastos.value
    } else {
      return gastos.value.filter(gastoState => gastoState.categoria === filtro.value)
    }
  })
</script>

<template>
  <div :class="{fijar: modal.mostrar}">
    <header>
      <h1>Planificador de gastos</h1>
      <div class="contenedor-header contenedor sombra">
        <Presupuesto
          v-if="!presupuesto"
          @definir-presupuesto="definirPresupuesto"
        />
        <ControlPresupuesto
          v-if="presupuesto"
          :presupuesto="presupuesto"
          :disponible="disponible"
          :gastado="gastado"
          @resetear-app="resetearApp"
        />
      </div>
    </header>
    <main v-if="presupuesto">
      <Filtro 
        v-model:filtro="filtro"
      />
      <div class="listado-gastos contenedor">
        <h2 v-if="filtroGastos.length === 0">No hay gastos</h2>
        <h2 v-else>Gastos {{ filtro }}</h2>
        <Gasto 
          v-for="gasto in filtroGastos"
          :gasto="gasto"
          @editar-gasto="editarGasto"
        />
      </div>
      <div class="crear-gasto">
        <img 
          :src="iconoNuevoGasto"
          @click="mostrarModal"
        />
      </div>
      <Modal
        v-if="modal.mostrar"
        :modal="modal"
        :disponible="disponible"
        @cerrar-modal="cerrarModal"
        @crear-gasto="crearGasto"
        @eliminar-gasto="eliminarGasto"
        v-model:id="gasto.id"
        v-model:nombre="gasto.nombre"
        v-model:categoria="gasto.categoria"
        v-model:fecha="gasto.fecha"
        v-model:cantidad="gasto.cantidad"
      />
    </main>
  </div>
</template>

<style>
  :root {
    --azul: #3b82f6;
    --blanco: #fff;
    --gris-claro: #f5f5f5;
    --gris: #94a3b8;
    --gris-oscuro: #64748b;
    --negro: #000;
  }

  html {
    font-size: 62.5%;
    box-sizing: border-box;
  }
  *,
  *:before,
  *:after {
    box-sizing: inherit;
  }

  body {
    font-size: 1.6rem;
    font-family: "Lato", sans-serif;
    background-color: var(--gris-claro);
  }

  h1 {
    font-size: 4rem;
  }

  h2 {
    font-size: 3rem;
  }
  .fijar {
    overflow: hidden;
    height: 100vh;
  }

  header {
    background-color: var(--azul);
  }

  header h1 {
    padding: 3rem 0;
    margin: 0;
    color: var(--blanco);
    text-align: center;
  }
  .contenedor {
    width: 90%;
    max-width: 80rem;
    margin: 0 auto;
  }

  .contenedor-header{
    margin-top: -5rem;
    transform: translateY(5rem);
    padding: 5rem;
  }

  .sombra {
    box-shadow: 0px 10px 15px -3px rgba(0,0,0,0.1);
    background-color: var(--blanco);
    border-radius: 1.2rem;
    padding: 5rem;
  }

  .crear-gasto {
    position: fixed;
    bottom: 5rem;
    right: 5rem;
  }

  .crear-gasto img {
    width: 5rem;
    cursor: pointer;
  }

  .listado-gastos {
    margin-top: 10rem;
  }

  .listado-gastos h2 {
    font-weight: 900;
    color: var(--gris-oscuro);
  }
</style>
