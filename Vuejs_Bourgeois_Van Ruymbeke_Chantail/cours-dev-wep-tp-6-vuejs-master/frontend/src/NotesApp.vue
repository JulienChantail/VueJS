<script setup>
import { ref, onMounted } from 'vue';
import Note from './Note.vue';
import CreateNote from './CreateNote.vue';
import { HOST } from './config.js';

const notesList = ref([]);

// Charger les notes
onMounted(async () => {
  notesList.value = await (await fetch(`${HOST}/notes`)).json();
});

// Ajouter une nouvelle note
const addNote = async (title, status) => {
  try {
    const response = await fetch(`${HOST}/notes`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ title, status }),
    });

    if (response.ok) {
      const newNote = await response.json();
      notesList.value.push(newNote);
    }
  } catch (error) {
    console.error('Error adding note:', error);
  }
};

// Supprimer une note
const deleteNote = async (noteId) => {
  try {
    const response = await fetch(`${HOST}/notes/${noteId}`, {
      method: 'DELETE',
    });

    if (response.ok) {
      notesList.value = notesList.value.filter((note) => note.id !== noteId);
    }
  } catch (error) {
    console.error('Error deleting note:', error);
  }
};
</script>


<template>
  <div class="notes-container">
    <Note
      v-for="note in notesList"
      :key="note.id"
      :note="note"
      @delete-note="deleteNote"
    ></Note>

    <CreateNote @create-note="addNote"></CreateNote>
  </div>
</template>



<style lang="scss" scoped>
@use 'assets/mediaQueryScreens.scss' as *;

$gutter-size: 15px;

.notes-container {
  display: flex;
  flex-wrap: wrap;
  gap: $gutter-size;

  margin-right: auto;
  margin-left: auto;

  border: 1px solid gray;
  border-radius: 10px;

  padding: $gutter-size;
}

@include smallScreen {
  .notes-container {
    width: 90vw;
  }

}

@include mediumScreen {
  .notes-container {
    width: 80vw;
  }

}

@include largeScreen {
  .notes-container {
    width: 1024px;
  }
}
</style>
