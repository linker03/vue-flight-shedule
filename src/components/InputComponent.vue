<script>
export default {
  props: {
    label: {
      type: String,
      required: true
    },
    modelValue: {
      type: String
    }
  }
}
</script>

<template>
  <label class="input-label">
    {{ label }}
    <input
      class="input"
      :value="modelValue"
      @input="$emit('update:modelValue', $event.target.value)"
      @blur="$emit('renderSheduleCanvas')"
    />
  </label>
</template>

<style>
.input-label {
  display: flex;
  flex-direction: column;
  background-color: var(--input-color);
  padding: 8px 16px;
  font-size: 12px;
  transition: 0.3s background-color ease-in-out;
  border-radius: 4px;
}

.input-label:focus-within {
  background-color: var(--input-focus-color);
}

.input {
  color: var(--secondary-text-color);
  font-size: 16px;
}
</style>
