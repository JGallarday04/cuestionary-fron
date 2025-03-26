<script setup>
import { ref } from 'vue';
import Bienvenida from './components/Bienvenida.vue';
import Cuestionario from './components/Cuestionario.vue';

const showQuestionnaire = ref(false);
const userName = ref('');

const startQuiz = (name) => {
  userName.value = name;
  showQuestionnaire.value = true;
};
</script>

<template>
  <div>
    <Bienvenida v-if="!showQuestionnaire" @start="startQuiz" />
    <Cuestionario v-else :nombre="userName" />
  </div>
</template>