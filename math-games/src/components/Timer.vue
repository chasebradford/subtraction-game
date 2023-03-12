<script setup>
import { ref, computed, onMounted } from 'vue'

const remaining = ref(180);
const seconds = computed(() => String(remaining.value % 60).padStart(2, '0'));
const minutes = computed(() => String(Math.floor(remaining.value / 60)).padStart(2, '0'));

const emit = defineEmits(['end'])

function update() {
  if (remaining.value == 0) {
    emit('end');
  } else {
    remaining.value -= 1;
    setTimeout(update, 1000);
  }
}
onMounted(update);
</script>

<template>

<div class="badge badge-danger timer">
  {{minutes}}:{{seconds}}
</div>

</template>

<style scoped>
.timer {
  font-size: 3em;
  width: 175px;
}

</style>

