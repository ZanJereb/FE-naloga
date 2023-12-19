<template>
  <div>
    <div class="pb-8 min-h-[40px]" style="display: flex; flex-flow; row; align-items: center; gap: 32px;">
      <div>
        <button @click="back">
          <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" data-slot="icon" class="w-6 h-6">
            <path stroke-linecap="round" stroke="#ffffffd1" stroke-linejoin="round" d="M10.5 19.5 3 12m0 0 7.5-7.5M3 12h18" />
          </svg>
        </button>
      </div>
      <div>
        <h1>Bucket</h1>
      </div>
      <div>
        <span style="color: #9d9e91;">{{ id.id }}</span>
      </div>
    </div>
    <div>
      <bucket-files-table :uuid="id"/>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue';
import { useRouter, useRoute } from 'vue-router'
import BucketFilesTable from '../../components/BucketFilesTable.vue';

export default defineComponent({
	components: { BucketFilesTable },
  setup() {
    const route = useRoute();
    const router = useRouter();

    const id = route.params;

    function back() {
      router.push('/');
    }
    return {
      id,
      back
    }
  }
})
</script>

<style>
</style>