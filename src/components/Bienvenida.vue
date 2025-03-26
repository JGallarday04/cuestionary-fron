<script setup>
import { ref } from 'vue';
import { useMotion } from '@vueuse/motion';

const emit = defineEmits(['start']);

const nombre = ref('');
const textMotion = useMotion();
const inputMotion = useMotion();
const buttonMotion = useMotion();

// Animación de cambio de color de fondo
const background = ref({
  backgroundColor: '#1a1a40',
  transition: 'background-color 2s ease-in-out'
});

setInterval(() => {
  background.value.backgroundColor = background.value.backgroundColor === '#1a1a40' ? '#292952' : '#1a1a40';
}, 2000);

// Función para comenzar el cuestionario
const comenzar = () => {
  if (nombre.value.trim() !== '') {
    emit('start', nombre.value);
  }
};
</script>

<template>
  <div class="background">
    <div class="container" :style="background">
      <div class="welcome">
        <h1 ref="textMotion" class="welcome-text">¡Bienvenido al Cuestionario!</h1>
        <input
          ref="inputMotion"
          v-model="nombre"
          type="text"
          placeholder="Ingresa tu nombre"
          class="name-input"
        />
        <button ref="buttonMotion" class="start-btn" @click="comenzar">Empezar</button>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* Evitar scroll en toda la página */
html, body {
  height: 100%;
  overflow: hidden;
  margin: 0;
  padding: 0;
}

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
}

/* Contenedor centrado */
.container {
  position: fixed;
  top: 0;
  left: 0;
  height: 100vh;
  width: 100vw;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: background-color 2s ease-in-out;
}

/* Sección de bienvenida */
.welcome {
  text-align: center;
  background: rgba(255, 255, 255, 0.1);
  padding: 30px;
  border-radius: 10px;
  backdrop-filter: blur(10px);
}

/* Título */
.welcome-text {
  font-size: 2.5rem;
  font-weight: bold;
  color: white;
  margin-bottom: 20px;
}

/* Input de nombre */
.name-input {
  padding: 12px;
  font-size: 1.2rem;
  border-radius: 5px;
  border: none;
  outline: none;
  width: 100%;
  max-width: 300px;
  text-align: center;
  margin-bottom: 15px;
}

/* Botón de inicio */
.start-btn {
  background: linear-gradient(135deg, #ff7eb3, #ff758c);
  border: none;
  padding: 12px 24px;
  font-size: 1.5rem;
  font-weight: bold;
  color: white;
  border-radius: 8px;
  cursor: pointer;
  transition: transform 0.2s ease-in-out, box-shadow 0.2s ease-in-out;
}

.start-btn:hover {
  transform: scale(1.1);
  box-shadow: 0px 4px 10px rgba(255, 120, 140, 0.4);
}
</style>
