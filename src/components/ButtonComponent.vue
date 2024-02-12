<script setup>
defineProps({
  label: {
    type: String,
    required: true
  }
})
</script>

<template>
  <button class="btn">
    {{ label }}
  </button>
</template>

<style>
.btn {
  padding: 10px 14px;
  border-radius: 4px;
  background-color: var(--secondary-color);
  font-size: 14px;
  text-transform: uppercase;
  font-weight: 600;
  color: rgba(0, 0, 0, 0.87);
}
</style>
