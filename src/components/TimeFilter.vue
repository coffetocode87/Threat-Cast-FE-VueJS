<template>
  <div class="time-filter">
    <select v-model.number="selected" @change="emitChange">
      <option :value="1">Last 24 Hours</option>
      <option :value="7">Last 7 Days</option>
      <option :value="30">Last 30 Days</option>
    </select>
  </div>
</template>

<script setup>
import { ref } from "vue";

const emit = defineEmits(["change"]);

const selected = ref(7);

const emitChange = () => {
  emit("change", selected.value);
};
</script>

<style>
.time-filter {
  margin-bottom: 20px;
}

select {
  padding: 8px;
  border-radius: 6px;
  background: #1e293b;
  color: white;
  border: 1px solid #334155;
}
</style>
