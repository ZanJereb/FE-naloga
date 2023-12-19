<template>
  <div class="myInputWrap">
    <input
      type="text"
      class="myInput"
      :placeholder="placeholder"
      @input="search"
    />
  </div>
</template>

<script setup>
const emit = defineEmits(['search']);
const props = defineProps(['placeholder'])

const search = (e) => {
  emit('search', e.target.value);
}

</script>

<style>

</style>