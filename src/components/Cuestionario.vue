<script setup>
import { ref, onMounted, defineProps, computed } from "vue";
import axios from "axios";

const props = defineProps({
  nombre: String
});

const preguntas = ref([]);
const respuestas = ref({});
const resultado = ref("");
const paginaActual = ref(1);

const cargarPreguntas = async () => {
  try {
    const response = await fetch("/preguntas.json");
    preguntas.value = await response.json();
  } catch (error) {
    console.error("Error cargando preguntas:", error);
  }
};

onMounted(cargarPreguntas);

// Devuelve las preguntas de la página actual (5 por página) y agrega índice absoluto
const preguntasFiltradas = computed(() => {
  const start = (paginaActual.value - 1) * 5;
  return preguntas.value.slice(start, start + 5).map((pregunta, index) => ({
    ...pregunta,
    indexAbsoluto: start + index
  }));
});

const avanzarPagina = () => {
  if (paginaActual.value < Math.ceil(preguntas.value.length / 5)) {
    paginaActual.value++;
  }
};

const retrocederPagina = () => {
  if (paginaActual.value > 1) {
    paginaActual.value--;
  }
};

const enviarRespuestas = async () => {
  let puntaje = 0;
  preguntas.value.forEach((pregunta, index) => {
    if (respuestas.value[index] === pregunta.respuestaCorrecta) {
      puntaje++;
    }
  });

  resultado.value = `Tu puntaje es ${puntaje} de ${preguntas.value.length}`;

  try {
    await axios.post("http://localhost:3000/api/respuestas", {
      nombre_usuario: props.nombre,
      respuestas: respuestas.value,
      puntaje: puntaje,
    });
    alert("Respuestas enviadas correctamente");
  } catch (error) {
    console.error("Error al enviar respuestas:", error);
  }
};
</script>

<template>
  <div class="background">
    <div class="container">
      <h2>Hola, {{ nombre }}. ¡Bienvenido al cuestionario!</h2>

      <div v-if="preguntas.length > 0">
        <div v-for="(pregunta) in preguntasFiltradas" :key="pregunta.indexAbsoluto" class="pregunta">
          <p>{{ pregunta.texto }}</p>
          <label v-for="(opcion, i) in pregunta.opciones" :key="i" class="opcion">
            <input type="radio" :name="'pregunta-' + pregunta.indexAbsoluto" v-model="respuestas[pregunta.indexAbsoluto]" :value="opcion">
            {{ opcion }}
          </label>
        </div>

        <div class="botones">
          <button v-if="paginaActual > 1" @click="retrocederPagina">Anterior</button>
          <button v-if="paginaActual < Math.ceil(preguntas.length / 5)" @click="avanzarPagina">Siguiente</button>
          <button v-if="paginaActual === Math.ceil(preguntas.length / 5)" @click="enviarRespuestas">Enviar</button>
        </div>

        <p v-if="resultado" class="resultado">{{ resultado }}</p>
      </div>
      <p v-else>Cargando preguntas...</p>
    </div>
  </div>
</template>

<style scoped>
/* Fondo ocupa toda la pantalla */
.background {
  position: fixed;
  top: 0;
  left: 0;
  height: 100vh;
  width: 100vw;
  display: flex;
  justify-content: center;
  align-items: center;
  background: linear-gradient(135deg, #1e3c72, #2a5298);
}

/* Permitir scroll en dispositivos móviles */
@media (max-width: 768px) {
  .background {
    overflow-y: auto;
    padding: 20px 10px;
    align-items: flex-start;
  }
}

/* Contenedor principal */
.container {
  text-align: center;
  width: 90%;
  max-width: 600px;
  padding: 20px;
  background: rgba(255, 255, 255, 0.9);
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
  color: black;
  display: flex;
  flex-direction: column;
  justify-content: center;
  height: auto;
  min-height: 90vh;
}

/* Ajustes para móviles */
@media (max-width: 768px) {
  .container {
    height: auto;
    min-height: unset;
    padding: 15px;
  }
}

/* Preguntas */
.pregunta {
  background: white;
  padding: 15px;
  margin: 10px 0;
  border-radius: 8px;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
  text-align: left;
}

/* Opciones */
.opcion {
  display: block;
  margin: 5px 0;
}

/* Botones */
.botones {
  margin-top: 20px;
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 10px;
}

button {
  background: #007bff;
  color: white;
  padding: 10px 20px;
  font-size: 1rem;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

button:hover {
  background: #0056b3;
}

/* Resultado */
.resultado {
  margin-top: 15px;
  font-size: 1.2rem;
  font-weight: bold;
  color: green;
}
</style>
