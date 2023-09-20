<script setup>
import { ref, reactive, onMounted, watch } from "vue";
import Header from "./components/Header.vue";
import Formulario from "./components/Formulario.vue";
import Paciente from "./components/Paciente.vue";
import { uid } from "uid";

const pacientes = ref([]);

const paciente = reactive({
  id: null,
  nombre: "",
  propietario: "",
  email: "",
  alta: "",
  sintomas: "",
});

onMounted(() => {
  const existenPacientesStorage = localStorage.getItem("pacientes");
  if (existenPacientesStorage) {
    pacientes.value = JSON.parse(existenPacientesStorage);
  }
});

watch(
  pacientes,
  () => {
    guardarStorage();
  }, // lo va revisar de una forma más estricta
  {
    deep: true,
  }
);

const guardarStorage = () => {
  localStorage.setItem("pacientes", JSON.stringify(pacientes.value));
};

const guardarPaciente = () => {
  if (paciente.id) {
    const { id } = paciente;
    const i = pacientes.value.findIndex((pac) => pac.id === id);
    pacientes.value[i] = { ...paciente };
  } else {
    pacientes.value.push({ ...paciente, id: uid() });
  }

  // reiniciar formulario
  // paciente.nombre = "";
  // paciente.propietario = "";
  // paciente.email = "";
  // paciente.alta = "";
  // paciente.sintomas = "";

  // otra forma
  Object.assign(paciente, {
    id: null,
    nombre: "",
    propietario: "",
    email: "",
    alta: "",
    sintomas: "",
  });
};

const actualizarPaciente = (id) => {
  const pacienteEditar = pacientes.value.find((pac) => pac.id === id);
  if (pacienteEditar) {
    Object.assign(paciente, pacienteEditar);
  }
};

const eliminarPaciente = (id) => {
  const pacienteEliminar = pacientes.value.find((pac) => pac.id === id);
  if (pacienteEliminar) {
    pacientes.value = pacientes.value.filter((pac) => pac.id !== id);
  }
};
</script>

<template>
  <div class="container mx-auto mt-20">
    <Header />
    <div class="mt-12 md:flex">
      <Formulario
        v-model:nombre="paciente.nombre"
        v-model:propietario="paciente.propietario"
        v-model:email="paciente.email"
        v-model:alta="paciente.alta"
        v-model:sintomas="paciente.sintomas"
        :id="paciente.id"
        @guardar-paciente="guardarPaciente"
      />

      <div class="md:w-1/2 md:h-screen overflow-y-auto">
        <h3 class="font-black text-3xl text-center">
          Administra tus Pacientes
        </h3>

        <div v-if="pacientes.length > 0">
          <p class="text-lg mt-5 text-center mb-10">
            Información de
            <span class="text-indigo-600 font-bold">Pacientes</span>
          </p>
          <Paciente
            v-for="pac in pacientes"
            :paciente="pac"
            @actualizar-paciente="actualizarPaciente"
            @eliminar-paciente="eliminarPaciente"
          />
        </div>
        <p v-else class="mt-10 text-2xl text-center">No Hay Pacientes</p>
      </div>
    </div>
  </div>
</template>
