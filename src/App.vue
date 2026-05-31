<script>
import NoteCard from './components/NoteCard.vue';
import NoteForm from './components/NoteForm.vue';

export default {
  name: 'App',
  components: {
    NoteCard,
    NoteForm 
  },
  data() {
    return { 
      notes: [] 
    };
  },
  created() {
    var saved = localStorage.getItem('pinboard-notes');
    if (saved) {
      try {
        var parsed = JSON.parse(saved);
        for (var i = 0; i < parsed.length; i++) {
          parsed[i].createdAt = new Date(parsed[i].createdAt);
        }
        this.notes = parsed;
      } catch (e) {
        this.notes = [];
      }
    }
  },
  computed: {
    sortedNotes() {
      var notesCopy = this.notes.slice();
      notesCopy.sort(function(a, b) {
        var order = { 'todo': 1, 'in-progress': 2, 'done': 3 };
        return order[a.status] - order[b.status];
      });
      return notesCopy;
    },
    importantNotes() {
      var result = [];
      for (var i = 0; i < this.sortedNotes.length; i++) {
        if (this.sortedNotes[i].important === true) {
          result.push(this.sortedNotes[i]);
        }
      }
      return result;
    },
    otherNotes() {
      var result = [];
      for (var i = 0; i < this.sortedNotes.length; i++) {
        if (this.sortedNotes[i].important !== true) {
          result.push(this.sortedNotes[i]);
        }
      }
      return result;
    },
    todoCount() {
      var cnt = 0;
      for (var i = 0; i < this.notes.length; i++) {
        if (this.notes[i].status === 'todo') cnt++;
      }
      return cnt;
    },
    inProgressCount() {
      var cnt = 0;
      for (var i = 0; i < this.notes.length; i++) {
        if (this.notes[i].status === 'in-progress') cnt++;
      }
      return cnt;
    },
    doneCount() {
      var cnt = 0;
      for (var i = 0; i < this.notes.length; i++) {
        if (this.notes[i].status === 'done') cnt++;
      }
      return cnt;
    },
    importantCount() {
      var cnt = 0;
      for (var i = 0; i < this.notes.length; i++) {
        if (this.notes[i].important) cnt++;
      }
      return cnt;
    }
  },
  methods: {
    handleAddNote(newNote) {
      this.notes.unshift(newNote);
      this.saveNotes();
    },
    handleDeleteNote(noteId) {
      var index = -1;
      for (var i = 0; i < this.notes.length; i++) {
        if (this.notes[i].id === noteId) { index = i; break; }
      }
      if (index !== -1) {
        this.notes.splice(index, 1);
        this.saveNotes();
      }
    },
    handleChangeStatus(payload) {
      var id = payload.id;
      var newStatus = payload.newStatus;
      var note = null;
      for (var i = 0; i < this.notes.length; i++) {
        if (this.notes[i].id === id) { note = this.notes[i]; break; }
      }
      if (note) {
        note.status = newStatus;
        if (newStatus === 'done') note.color = 'success';
        else if (newStatus === 'in-progress') note.color = 'warning';
        else note.color = 'danger';
        this.saveNotes();
      }
    },
    handleToggleImportant(noteId) {
      var note = null;
      for (var i = 0; i < this.notes.length; i++) {
        if (this.notes[i].id === noteId) { note = this.notes[i]; break; }
      }
      if (note) {
        note.important = !note.important;
        this.saveNotes();
      }
    },
    saveNotes() {
      localStorage.setItem('pinboard-notes', JSON.stringify(this.notes));
    }
  }
};
</script>

<template>
  <div class="container py-4">
    <h1 class="text-center mb-4">📌 Доска заметок</h1>
    <NoteForm @add-note="handleAddNote" />
    <div class="text-center text-md-start mb-3">
      <p class="text-muted">
        Всего: <strong>{{ notes.length }}</strong>
        🔴 <strong>{{ todoCount }}</strong>
        🟡 <strong>{{ inProgressCount }}</strong>
        🟢 <strong>{{ doneCount }}</strong>
        ⭐ <strong>{{ importantCount }}</strong>
      </p>
    </div>
    <div v-if="importantNotes.length > 0" class="mb-4">
      <h4 class="mb-3">⭐ Важные</h4>
        <div class="row">
            <div v-for="note in importantNotes" :key="note.id" class="col-12 col-md-6 col-lg-4">
              <NoteCard :note="note" @delete-note="handleDeleteNote" @change-status="handleChangeStatus" @toggle-important="handleToggleImportant" />
            </div>
        </div>
    </div>
    <div class="row">
      <div v-for="note in otherNotes" :key="note.id" class="col-12 col-md-6 col-lg-4">
        <NoteCard :note="note" @delete-note="handleDeleteNote" @change-status="handleChangeStatus" @toggle-important="handleToggleImportant" />
      </div>
    </div>
    <div v-if="notes.length === 0" class="text-center text-muted mt-5">
      Доска пуста. Добавьте первую заметку!
    </div>
  </div>
</template>