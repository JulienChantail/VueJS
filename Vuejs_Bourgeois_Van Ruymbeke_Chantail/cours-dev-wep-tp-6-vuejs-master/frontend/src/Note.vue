<!-- eslint-disable vue/multi-word-component-names -->

<script setup>
import { ref, onMounted } from 'vue';
import { HOST } from './config.js';

// Déclarez les props pour ce composant
const props = defineProps({
  note: Object, // ou { type: Object, required: true }
});

const tasks = ref([]);
const newTaskContent = ref('');

// Charger les tâches d'une note
onMounted(async () => {
  try {
    const response = await fetch(`${HOST}/notes/${props.note.id}/tasks`);
    const fetchedTasks = await response.json();

    // Vérifier si chaque tâche a un id, sinon en créer un localement
    tasks.value = fetchedTasks.map(task => ({
      ...task,
      id: task.id || `temp-${Math.random()}` // Générer un id si nécessaire
    }));
  } catch (error) {
    console.error('Error fetching tasks:', error);
    alert('Failed to load tasks.');
  }
});

// Créer une nouvelle tâche
const createTask = async () => {
  if (!newTaskContent.value.trim()) {
    alert('Task content cannot be empty.');
    return;
  }

  try {
    const response = await fetch(`${HOST}/notes/${props.note.id}/tasks`, {
      method: 'POST',
      headers: {'Content-Type': 'application/json'},
      body: JSON.stringify({content: newTaskContent.value}),
    });

    if (response.ok) {
      const newTask = await response.json();

      // S'assurer que la tâche a un id
      newTask.id = newTask.id || `temp-${Math.random()}`; // Générer un id local si nécessaire

      tasks.value.push(newTask);
      newTaskContent.value = '';
    } else {
      alert('Failed to create task.');
    }
  } catch (error) {
    console.error('Error creating task:', error);
    alert('An error occurred while creating the task.');
  }
};


// Supprimer une tâche
const deleteTask = async (taskId) => {
  try {
    console.log('Attempting to delete task with ID:', taskId);

    const response = await fetch(`${HOST}/tasks/${taskId}`, {
      method: 'DELETE',
    });

    if (response.ok) {
      console.log('Successfully deleted task from backend.');
      console.log('Tasks before deletion:', tasks.value);

      // Update local tasks array
      tasks.value = tasks.value.filter((task) => task.id !== taskId);

      console.log('Tasks after deletion:', tasks.value);
    } else {
      const errorText = await response.text();
      console.error('Failed to delete task from backend:', response.status, response.statusText, errorText);
      alert(`Failed to delete task: ${response.status} ${response.statusText}`);
    }
  } catch (error) {
    console.error('Error deleting task:', error);
    alert('An error occurred while deleting the task.');
  }
};

</script>

<template>
  <div :class="['note', props.note.status]">
    <div class="delete-button" @click="$emit('delete-note', props.note.id)">&#x1F5D1;</div>
    <h2>{{ props.note.title }}</h2>

    <div class="tasks">
      <!-- Liste des tâches -->
      <div
        v-for="task in tasks"
        :key="task.id"
        class="task"
      >
        <div class="content">{{ task.content }}</div>
        <div class="delete-button" @click="deleteTask(task.id)">&#x1F5D1;</div>
      </div>

      <!-- Ajouter une nouvelle tâche -->
      <div class="new-task">
        <input
          class="content"
          v-model="newTaskContent"
          @keyup.enter="createTask"
          placeholder="Enter new task..."
        >
        <button class="create-btn" @click="createTask">+</button>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
@use 'assets/mediaQueryScreens.scss' as *;
@use 'assets/colors.scss' as *;

$gutter-size: 15px;

.note {
  min-height: 200px;
  border-radius: 10px;
  padding: 10px;
  color: white;
  position: relative;

  & > .delete-button {
    position: absolute;
    top: 5px;
    right: 10px;
    font-size: 25px;
    cursor: pointer;
  }

  h2 {
    padding-bottom: 5px;
    width: calc(100% - 25px);
  }

  .task {
    padding: 10px 5px 10px 5px;
    margin-bottom: 10px;
  }

  .task {
    border-radius: 5px;
    display: flex;
    align-items: center;

    & > .content {
      flex-grow: 1;
    }

    & > .delete-button {
      visibility: hidden;
      flex-grow: 0;
      color: black;
      font-size: 20px;
      cursor: pointer;
    }

    &:hover > .delete-button {
      visibility: visible;
    }
  }

  .new-task {
    display: flex;

    & > input {
      flex-grow: 1;
      border: 0;
      padding: 15px 5px 15px 5px;

      &::placeholder {
        color: white;
      }
    }

    & > .create-btn {
      background-color: white;
      font-size: 25px;
      font-weight: bold;
      width: 40px;
    }
  }

  &.unimportant {
    background-color: $dark-green;

    .task, .new-task > input {
      background-color: $light-green;
      color: darken($dark-green, 20%);
    }

    .new-task > .create-btn {
      color: $dark-green;
    }
  }

  &.serious {
    background-color: $dark-yellow;

    .task, .new-task > input {
      background-color: $light-yellow;
      color: darken($dark-yellow, 20%);
    }

    .new-task > .create-btn {
      color: $dark-yellow;
    }
  }

  &.urgent {
    background-color: $dark-red;

    .task, .new-task > input {
      background-color: $light-red;
      color: darken($dark-red, 20%);
    }

    .new-task > .create-btn {
      color: $dark-red;
    }
  }
}

@include smallScreen {
  .note {
    width: 100%;
  }
}

@include mediumScreen {
  .note {
    width: calc((100% - $gutter-size) / 2);
  }
}

@include largeScreen {
  .note {
    width: calc((100% - ($gutter-size * 2)) / 3);
  }
}
</style>

<!-- Désactiver ESLint localement pour ce composant -->
<!-- eslint-disable vue/multi-word-component-names -->
