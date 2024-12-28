<!-- eslint-disable vue/multi-word-component-names -->

<template>
  <div class="note-status-filter">
    <div
      v-for="status in filterOptions"
      :key="status"
      :class="['filter-option', { selected: selectedStatus === status }]"
      @click="selectStatus(status)"
    >
      {{ status }}
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';

// Définir les options de filtre
const filterOptions = ['All', 'Unimportant', 'Serious', 'Urgent'];

// État pour suivre le statut sélectionné
const selectedStatus = ref('All');

// Déclaration explicite des événements personnalisés avec `defineEmits`
const emit = defineEmits(['filter-change']);

// Émettre le changement de statut à l'application parent
const emitStatusChange = (status) => {
  selectedStatus.value = status;
  emit('filter-change', status);  // Utiliser 'emit' pour émettre l'événement
};

// Lorsque l'utilisateur clique sur un statut, changer le statut sélectionné
const selectStatus = (status) => {
  emitStatusChange(status);
};
</script>

<style scoped>
.note-status-filter {
  display: flex;
  gap: 10px;
}

.filter-option {
  padding: 5px 10px;
  cursor: pointer;
  border-radius: 5px;
  transition: background-color 0.2s;
}

.filter-option.selected {
  background-color: #007bff;
  color: white;
}

.filter-option:hover {
  background-color: #f0f0f0;
}
</style>
