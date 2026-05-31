<script>
export default {
  name: 'NoteCard',
  props: {
    note: {
      type: Object,
      required: true 
    }
  },
  computed: {
    statusLabel() {
      if (this.note.status === 'done') return 'Выполнена';
      if (this.note.status === 'in-progress') return 'В процессе';
      return 'Не выполнена';
    },
    nextStatus() {
      if (this.note.status === 'todo') return 'in-progress';
      if (this.note.status === 'in-progress') return 'done';
      return 'todo';
    },
    nextStatusLabel() {
      if (this.nextStatus === 'in-progress') return '🟡 В процесс';
      if (this.nextStatus === 'done') return '🟢 Завершить';
      return '🔴 Вернуть';
    },
    formattedDate() {
      if (!this.note.createdAt) return '';
      var d = new Date(this.note.createdAt);
      var day = d.getDate();
      var month = d.getMonth() + 1;
      var year = d.getFullYear();
      var hours = d.getHours();
      var minutes = d.getMinutes();
      if (minutes < 10) minutes = '0' + minutes;
      return day + '.' + month + '.' + year + ' ' + hours + ':' + minutes;
    }
  },
  methods: {
    changeStatus() {
      this.$emit('change-status', { id: this.note.id, newStatus: this.nextStatus });
    }
  }
};
</script>

<template>
  <div class="card mb-3 text-white" :class="'bg-' + note.color">
    <div class="card-body">
      <h5 class="card-title">
        {{ note.text }}
        <span v-if="note.important" class="badge bg-warning text-dark">⭐ Важное</span>
      </h5>
      <p class="card-text small">
        <span class="badge bg-light text-dark">{{ statusLabel }}</span>
        <span class="ms-2">{{ formattedDate }}</span>
      </p>
      <div class="d-flex gap-2 flex-wrap">
        <button class="btn btn-sm btn-light" @click="changeStatus">{{ nextStatusLabel }}</button>
        <button class="btn btn-sm btn-outline-light" @click="$emit('toggle-important', note.id)">
          {{ note.important ? 'Убрать из важных' : '★ В важные' }}
        </button>
        <button class="btn btn-sm btn-outline-light" @click="$emit('delete-note', note.id)">Удалить</button>
      </div>
    </div>
  </div>
</template>
