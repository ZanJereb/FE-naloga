<template>
  <div v-show="show" class="modal-mask">
    <div class="modal-wrapper">
      <div class="modal-container">

        <div class="modal-header">
          <slot name="header">
            default header
          </slot>
        </div>

        <div class="modal-body">
          <slot name="body">
            default body
          </slot>
        </div>

        <div>
          <slot name="footer">
            <button class="addButton" @click="$emit('close')">
              OK
            </button>
          </slot>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">

  const props = defineProps(['show'])
  const emit = defineEmits(['close'])

  const close = () => {
  emit('close');
}
</script>

<style>

</style>