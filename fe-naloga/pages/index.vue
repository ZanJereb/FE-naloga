<template>
  <div>
    <div class="pb-8 min-h-[40px]" style="display: flex; flex-flow; row; justify-content: space-between; align-items: center; gap: 32px;">
      <div>
        <div>
          <h1>Storage</h1>
        </div>
      </div>
    </div>
    <div>
      <buckets-table />
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue';

export default defineComponent({
  setup() {
    return {
    }
  }
})
</script>

<style>
</style>