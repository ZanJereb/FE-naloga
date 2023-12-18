<template>
  <div class="myInputWrap">
    <input
      type="text"
      class="myInput"
      placeholder="Search files"
      @input="search"
    />
  </div>
</template>

<script setup>
const emit = defineEmits(['search']);

const search = (e) => {
  emit('search', e.target.value);
}

</script>

<style>

</style>