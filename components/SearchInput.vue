<template>
    <div class="w-full max-w-3xl mx-auto">
      <input
        type="text"
        v-model="query"
        @input="onInput"
        class="w-full p-4 border border-gray-300 rounded-lg text-lg"
        placeholder="Search..."
      />
    </div>
  </template>
  
  <script setup>
import { ref, defineEmits } from 'vue';

const query = ref('');
const emit = defineEmits(['input']);

const onInput = () => {
    emit('input', query.value);
};
</script>
  
  <style scoped>
  </style>