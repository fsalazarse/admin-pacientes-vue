<script setup>
  import Header from './components/header.vue'
  import {uid} from 'uid'
  import Formulario from './components/formulario.vue'
  import {ref, reactive, watch, onMounted} from 'vue'
  import Paciente from './components/Paciente.vue'

  // almacenar los pacientes en localStorage y coloar los pacientes del Storage en el state
  // crear una funcion, un watch y leer el Storage con el metodo onMounted

  //props

  //state

  const pacientes = ref([])
  
  const paciente = reactive({
    id : null,
    nombre: '',
    propietario: '',
    email: '',
    alta: '',
    sintomas:'',
  })
  
  //custom event
  const guardarPaciente = () => {
    if (paciente.id ) {
      const { id } = paciente
      const i = pacientes.value.findIndex((pacienteState) => pacienteState.id === id)
      pacientes.value[i] = {...paciente}
    }else {

      pacientes.value.push({
        ...paciente,
        id:uid()
      })
    }

    //resetear formulario
    // paciente.nombre = ""
    // paciente.propietario = ""
    // paciente.email = ""
    // paciente.alta = ""
    // paciente.sintomas = ""

    //Otra forma para resetar formulario
    Object.assign(paciente,{
      nombre: '',
      propietario: '',
      email: '',
      alta: '',
      sintomas:'',
      id: null
    })
  }

  //...... METHOD

  watch(pacientes, () => {
    guardarLocalStorage()
  }, {
    deep: true
  })

  const guardarLocalStorage = () => {
    localStorage.setItem("pacientes", JSON.stringify(pacientes.value))
  }

  onMounted(() => {
    console.log("Estot en onMounted");
    const pacientesStorage = localStorage.getItem('pacientes')

    if (pacientesStorage) {
      pacientes.value = JSON.parse(pacientesStorage)
    }
  })

  
  const actualizarPaciente = (id) => {
    const pacienteEditar = pacientes.value.filter(paciente => paciente.id === id)[0]
    Object.assign(paciente, pacienteEditar)
  }


  const eliminarPaciente = (id) => {
    console.log("fff");
    pacientes.value = pacientes.value.filter(paciente => paciente.id !== id)
  }



</script>

<template>
  <div class="container mx-auto mt-20">
    <Header />
    
    <div class="mt-12 md:flex">
      <Formulario 
      v-model:nombre        =   "paciente.nombre"
      v-model:propietario   =   "paciente.propietario"
      v-model:email         =   "paciente.email"
      v-model:alta          =   "paciente.alta"
      v-model:sintomas      =   "paciente.sintomas"
      @guardar-paciente     =   "guardarPaciente"
      :id                   =    "paciente.id"
      
      />
      

      <div class="md:w-1/2 md:h-screen overflow-y-scroll">
        <h3 class="font-black text-3xl text-center">Adminstrar tus pacientes</h3>
        <div v-if="pacientes.length > 0">
          <p class="text-lg mt-5 text-center mb-10">
            Añade Pacientes y 
            <span class="text-indigo-600 font-bold">Administralos</span>
         </p>
         <!-- 
          :paciente="paciente"
          creo un props para entregarselo al componente Paciente
        -->
          <Paciente 
            v-for="paciente in pacientes"
            :paciente             =   "paciente"
            @actualizar-paciente  =   "actualizarPaciente" 
            @eliminar-paciente    =   "eliminarPaciente"
          />
        </div>
        <div v-else>
            no hay pacientes
        </div>

      </div>
    </div>
  </div>

</template>


