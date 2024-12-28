<script setup>
import { ref, computed } from 'vue';

// Définir les événements émis par ce composant
const emit = defineEmits(['create-note']);

const newNoteTitle = ref('');
const newNoteStatus = ref('unimportant');

// Calculer la classe dynamique selon l'état
const noteClass = computed(() => `note ${newNoteStatus.value}`);

const createNote = () => {
  if (!newNoteTitle.value.trim()) {
    alert('Note title cannot be empty.');
    return;
  }
  // Émettre les données vers le parent
  emit('create-note', newNoteTitle.value, newNoteStatus.value);
  newNoteTitle.value = '';
};
</script>

<template>
  <div :class="noteClass">
    <input
      class="new-note-title"
      v-model="newNoteTitle"
      placeholder="Enter note title..."
    />
    <div class="status-select">
      <div>
        <input
          type="radio"
          id="unimportant"
          v-model="newNoteStatus"
          value="unimportant"
        />
        <label for="unimportant">Unimportant</label>
      </div>
      <div>
        <input
          type="radio"
          id="serious"
          v-model="newNoteStatus"
          value="serious"
        />
        <label for="serious">Serious</label>
      </div>
      <div>
        <input
          type="radio"
          id="urgent"
          v-model="newNoteStatus"
          value="urgent"
        />
        <label for="urgent">Urgent</label>
      </div>
    </div>
    <button class="create-btn" @click="createNote">Create new note</button>
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

  & > input {
    padding: 15px 5px 15px 5px;
    border: 0;
    font-size: 20px;
    width: 100%;
    border-radius: 5px;

    &::placeholder {
      font-style: italic;
      color: white;
    }
  }

  & > .create-btn {
    padding: 10px 5px 10px 5px;
    width: 100%;
    background-color: white;
    color: $dark-green;
    font-weight: bold;
    font-size: 15px;
  }

  &.unimportant {
    background-color: $dark-green;
    & > input {
      background-color: $light-green;
      color: darken($dark-green, 20%);

      &::placeholder {
        color: darken($light-green, 20%); // Assurer un bon contraste avec le fond
      }
    }
  }

  &.serious {
    background-color: $dark-yellow;
    & > input {
      background-color: $light-yellow;
      color: darken($dark-yellow, 20%);

      &::placeholder {
        color: darken($light-yellow, 20%); // Assurer un bon contraste avec le fond
      }
    }
  }

  &.urgent {
    background-color: $dark-red;
    & > input {
      background-color: $light-red;
      color: darken($dark-red, 20%);

      &::placeholder {
        color: darken($light-red, 20%); // Assurer un bon contraste avec le fond
      }
    }
  }

  .status-select {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;

    margin-top: 10px;
    margin-bottom: 10px;

    &  input {
      margin-right: 5px;
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
