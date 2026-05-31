<script>
export default {
  name: 'NoteForm',
  data() {
    return {
      noteText: '',
      noteStatus: '',
      isImportant: false
    };
  },
  methods: {
    addNote() {
      if (!this.noteText.trim()) return;
      var status = this.noteStatus || 'todo';
      var color = '';
      if (status === 'done') color = 'success';
      else if (status === 'in-progress') color = 'warning';
      else color = 'danger';
      var newNote = {
        id: Date.now(),
        text: this.noteText.trim(),
        status: status,
        color: color,
        createdAt: new Date(),
        important: this.isImportant
      };
      this.$emit('add-note', newNote);
      this.noteText = '';
      this.noteStatus = '';
      this.isImportant = false;
    }
  }
};
</script>

<template>
  <div class="card mb-4">
    <div class="card-body">
      <h5 class="card-title">Добавить заметку</h5>

      <div class="mb-3">
        <input type="text" class="form-control" placeholder="Что нужно сделать?" v-model="noteText" />
      </div>

      <div class="mb-3">
        <select class="form-select" v-model="noteStatus">
          <option value="">Выбери статус</option>
          <option value="todo">🔴 Не выполнена</option>
          <option value="in-progress">🟡 В процессе</option>
          <option value="done">🟢 Выполнена</option>
        </select>
      </div>
      <div class="mb-3 form-check">
        <input type="checkbox" class="form-check-input" id="importantCheck" v-model="isImportant" />
        <label class="form-check-label" for="importantCheck">Важная заметка</label>
      </div>
      <button type="button" class="btn btn-primary" :disabled="!noteText.trim()" @click="addNote">
        Добавить
      </button>
    </div>
  </div>
</template>