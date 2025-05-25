<script setup>
    import { ref, reactive, watch, onMounted } from "vue"
    import { uid } from "uid"
    import Header from './components/Header.vue'
    import Formulario from './components/Formulario.vue'
    import Paciente from './components/Paciente.vue'

    const pacientes = ref([])

    const paciente = reactive({
        id: null,
        nombre: '',
        propietario: '',
        email: '',
        alta: '',
        sintomas: ''
    })

    watch(pacientes, () => {
        guardarLocalStorage()
    }, {
        deep: true
    })

    const guardarLocalStorage = () => {
        localStorage.setItem('pacientes', JSON.stringify(pacientes.value))
    }

    onMounted(() => {
        const pacientesStorage = localStorage.getItem('pacientes')
        if (pacientesStorage) {
            pacientes.value = JSON.parse(pacientesStorage)
        }
    })

    const guardarPaciente = () => {
        if (paciente.id) {
            const { id } = paciente
            const indice = pacientes.value.findIndex(_paciente => _paciente.id === id)
            pacientes.value[indice] ={...paciente}
        } else {
            pacientes.value.push({
                ...paciente,
                id: uid()
            })
        }

        Object.assign(paciente, {
            id: null,
            nombre: '',
            propietario: '',
            email: '',
            alta: '',
            sintomas: ''
        })
    }

    const actualizarPaciente = (id) => {
        const pacienteEditar = pacientes.value.filter( _paciente => _paciente.id === id)[0]
        Object.assign(paciente, pacienteEditar)
    }

    const eliminarPaciente = (id) => {
        pacientes.value = pacientes.value.filter( _paciente => _paciente.id !== id)
    }
</script>

<template>
    <div class="container mx-auto mt-5">
        <Header />
        <div class="mt-12 md:flex">
            <Formulario
                v-model:nombre="paciente.nombre"
                v-model:propietario="paciente.propietario"
                v-model:email="paciente.email"
                v-model:alta="paciente.alta"
                v-model:sintomas="paciente.sintomas"
                @guardar-paciente="guardarPaciente"
                :id="paciente.id"
            />
            <div class="md:w-1/2 md:h-screen overflow-y-scroll">
                <h2 class="font-black text-3xl text-center">Administra tus pacientes</h2>
                <p class=" text-lg mt-1 text-center mb-10">
                    Administra tus
                    <span class="text-indigo-600 font-bold">pacientes</span>
                </p>
                <div v-if="pacientes.length > 0">
                    <Paciente
                        v-for="paciente in pacientes"
                        :paciente="paciente"
                        @actualizar-paciente="actualizarPaciente"
                        @eliminar-paciente="eliminarPaciente"
                    />
                </div>
                <div v-else class="mt-10 uppercase font-bold text-center">No se han cargado pacientes.</div>
            </div>
        </div>
    </div>
</template>
