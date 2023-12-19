<template>
  <div>
    <div class="pb-8 min-h-[40px]" style="display: flex; flex-flow; row; justify-content: space-between; align-items: center; gap: 32px;">
      <div>
        <search-bar @search="handleSearch" placeholder="Search buckets"/>
      </div>
      <div style="display: flex; flex-flow; row; justify-content: space-between; align-items: center; gap: 32px;">
        <div>
          <button class="refreshButton" @click="fetchBuckets">
            <div class="flex row" style="align-items: center;">
              <svg 
                xmlns="http://www.w3.org/2000/svg"
                fill="none"
                viewBox="0 0 24 24"
                stroke-width="1.5"
                stroke="#ffffffd1"
                data-slot="icon"
                class="w-6 h-6"
              >
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  d="M16.023 9.348h4.992v-.001M2.985 19.644v-4.992m0 0h4.992m-4.993 0 3.181 3.183a8.25 8.25 0 0 0 13.803-3.7M4.031 9.865a8.25 8.25 0 0 1 13.803-3.7l3.181 3.182m0-4.991v4.99" />
              </svg>
              <span class="px-2">Refresh</span>
            </div>
          </button>
        </div>
        <div>
          <button class="addButton" @click="openAddBucket()">
            <div class="flex row" style="align-items: center;">
              <svg 
                xmlns="http://www.w3.org/2000/svg"
                fill="none"
                viewBox="0 0 24 24"
                stroke-width="1.5"
                stroke="#f9ff73"
                data-slot="icon"
                class="w-6 h-6"
              >
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  d="M2.25 12.75V12A2.25 2.25 0 0 1 4.5 9.75h15A2.25 2.25 0 0 1 21.75 12v.75m-8.69-6.44-2.12-2.12a1.5 1.5 0 0 0-1.061-.44H4.5A2.25 2.25 0 0 0 2.25 6v12a2.25 2.25 0 0 0 2.25 2.25h15A2.25 2.25 0 0 0 21.75 18V9a2.25 2.25 0 0 0-2.25-2.25h-5.379a1.5 1.5 0 0 1-1.06-.44Z" />
              </svg>
              <span class="px-2">New bucket</span>
            </div>
          </button>
        </div>
      </div>
    </div>
    <table class="min-w-full">
      <thead>
        <tr>
          <th colspan="1" rowspan="1">Bucket name</th>
          <th colspan="1" rowspan="1">Bucket type</th>
          <th colspan="1" rowspan="1">Bucket Uuid</th>
          <th colspan="1" rowspan="1">Storage usage</th>
          <th colspan="1" rowspan="1">Description</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="data in filteredBuckets" :key="data.bucketUuid" @click="navigateToBucket(data.bucketUuid)">
          <td>{{ data.name }}</td>
          <td>{{ convertBucketType(data.bucketType) }}</td>
          <td>{{ data.bucketUuid }}</td>
          <td>{{ convertSize(data.size) }}</td>
          <td>{{ data.description }}</td>
        </tr>
      </tbody>
    </table>

    <add-bucket-dialog :show="open" @close="addBucket">
      <template v-slot:header>
        <h3>New storage bucket</h3>
      </template>
      <template v-slot:body>
        <div class="grid gap-4">
          <div>
            <p>Enter the desired name and description for the bucket so you can easily recognise it.</p>
          </div>
          <div>
            <span>Bucket name*</span>
            <div class="myInputWrap">
              <input
                v-model="bucketName"
                type="text"
                class="myInput"
                placeholder="Bucket name"
              />
            </div>
          </div>
          <div>
            <span>Bucket description</span>
            <div class="myInputWrap">
              <textarea
                v-model="bucketDescription"
                type="text"
                class="myInput"
                placeholder="Bucket description"
              />
            </div>
          </div>
        </div>
      </template>
    </add-bucket-dialog>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted, computed } from 'vue';
import { useRouter } from 'vue-router'

import axios from 'axios';

export default defineComponent({
  setup() {
    const router = useRouter();

    const searchTable = ref('');
    
    const open = ref(false);
    const bucketName = ref('');
    const bucketDescription = ref('');

    var tableData = ref([]);

    onMounted(async ()=> {
      await fetchBuckets();
    })

    async function fetchBuckets() {
      await axios.get('https://api.apillon.io/storage/buckets',
        {
          'headers': {
            'Content-Type': 'application/json',
            'Authorization': `Basic ${btoa('f63ffc52-6dfd-414c-8ab9-6cc7d4a47551:W&2R2#m0i53R')}`
          }
        })
        .then((res) =>  {
          tableData.value = res.data.data.items;
        })
        .catch(e => {
          console.log(e);
        })
    }

    function openAddBucket() {
      open.value = true;
    }

    async function addBucket() {
      await axios.post('https://api.apillon.io/storage/buckets',
        {
          name: bucketName.value,
          description: bucketDescription.value,
        },
        {
          'headers': {
            'Content-Type': 'application/json',
            'Authorization': `Basic ${btoa('f63ffc52-6dfd-414c-8ab9-6cc7d4a47551:W&2R2#m0i53R')}`
          }
        })
        .then(() =>  {
          open.value = false;
          fetchBuckets();
        })
        .catch(e => {
          console.log(e);
        })
    }

    const filteredBuckets = computed(() => {
      if (!searchTable.value) {
        return tableData.value;
      } else {
        return tableData.value.filter(bucket => bucket.name.includes(searchTable.value));
      } 
    })

    const handleSearch = (search) => {
      searchTable.value = search;
    }

    function convertBucketType(bucketType): String{
      switch (bucketType) {
        case 1:
          return 'Storage';
        case 2:
          return 'Website';
        case 3:
          return 'NFT';
        default:
          return '';
      }
    }

    function calculateStorageUsage(size): string {
      const percent = (100 * size) / 3221225472;
      if (percent < 0.1) {
        return '(0%)'
      }
      return `(${percent.toFixed(2)}%)`;
    }

    function convertSize(size): String{
      var convertedSize = 0;
      switch (true) {
        case (0 <= size && size < 1024):
          return `${size} Bytes ${calculateStorageUsage(size)}`;
        case (1024 <= size && size < 1048576):
          convertedSize = size/1024;
          return `${convertedSize.toFixed(2)} KB ${calculateStorageUsage(size)}`;
        case (1048576 <= size && size < 1073741824):
          convertedSize = size/1048576;
          return `${convertedSize.toFixed(2)} MB ${calculateStorageUsage(size)}`;
        case (1073741824 <= size):
          convertedSize = size/1073741824;
          return `${convertedSize.toFixed(2)} GB ${calculateStorageUsage(size)}`;
        default:
          return '';
      }
    }

    function navigateToBucket(id) {
      router.push(`/bucket/${id}`);
    }

    return {
      filteredBuckets,
      open,
      bucketName,
      bucketDescription,
      openAddBucket,
      addBucket,
      fetchBuckets,
      handleSearch,
      convertBucketType,
      convertSize,
      navigateToBucket,
    }
  }
})
</script>

<style>

</style>