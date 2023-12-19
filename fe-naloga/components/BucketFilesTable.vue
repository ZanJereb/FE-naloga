<template>
  <div>
    <div class="pb-8 min-h-[40px]" style="display: flex; flex-flow; row; justify-content: space-between; align-items: center; gap: 32px;">
      <div>
        <search-bar @search="handleSearch" placeholder="Search files"/>
      </div>
      <div style="display: flex; flex-flow; row; justify-content: space-between; align-items: center; gap: 32px;">
        <div>
          <button class="refreshButton" @click="fetchBucketsConetent">
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
          <button class="addButton" @click="openUpload()">
            <div class="flex row" style="align-items: center;">
              <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="#f9ff73" data-slot="icon" class="w-6 h-6">
                <path stroke-linecap="round" stroke-linejoin="round" d="M12 10.5v6m3-3H9m4.06-7.19-2.12-2.12a1.5 1.5 0 0 0-1.061-.44H4.5A2.25 2.25 0 0 0 2.25 6v12a2.25 2.25 0 0 0 2.25 2.25h15A2.25 2.25 0 0 0 21.75 18V9a2.25 2.25 0 0 0-2.25-2.25h-5.379a1.5 1.5 0 0 1-1.06-.44Z" />
              </svg>
              <span class="px-2">Upload</span>
            </div>
          </button>
        </div>
      </div>
    </div>
    <table class="min-w-full table-auto">
      <thead>
        <tr>
          <th colspan="1" rowspan="1">File name</th>
          <th colspan="1" rowspan="1">File CID</th>
          <th colspan="1" rowspan="1">Download link</th>
          <th colspan="1" rowspan="1">File size</th>
          <th colspan="1" rowspan="1">Created</th>
          <th colspan="1" rowspan="1">Content type</th>
          <th colspan="1" rowspan="1">Status</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="data in filteredBucketsContent" :key="data.bucketUuid">
          <td>{{ data.name }}</td>
          <td>{{ data.CID }}</td>
          <td style="word-break: break-word;">
            <a :href="data.link">
              <span class="text-xs">{{ truncatedText(data.link) }}</span>
            </a>
          </td>
          <td>{{ (data.size) ? convertSize(data.size) : '' }}</td>
          <td>{{ convertTimeDate(data.createTime) }}</td>
          <td>{{ (data.type === 1) ? 'Directory' : 'File' }}</td>
          <td>{{ convertBucketType(data.fileStatus) }}</td>
        </tr>
      </tbody>
    </table>

    <add-bucket-dialog :show="open" @close="submitFile">
      <template v-slot:header>
        <h3>upload</h3>
      </template>
      <template v-slot:body>
        <div class="grid gap-4">
          <div>
            <span>File</span>
            <div class="myInputWrap">
              <input
                type="file"
                class="myInput"
                placeholder="File"
                @change="uploadFile"
                ref="file"
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

import axios from 'axios';

export default defineComponent({
  props: ['uuid'], 
  setup(props) {
    const searchTable = ref('');
    
    const open = ref(false);
    let file = null;
    var base64Data = '';

    var tableData = ref([]);

    onMounted(async ()=> {
      await fetchBucketsConetent();
    })

    async function fetchBucketsConetent() {
      await axios.get(`https://api.apillon.io/storage/buckets/${props.uuid.id}/content`,
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

    const filteredBucketsContent = computed(() => {
      if (!searchTable.value) {
        return tableData.value;
      } else {
        return tableData.value.filter(bucket => bucket.name.includes(searchTable.value));
      } 
    })

    const handleSearch = (search) => {
      searchTable.value = search;
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

    function convertTimeDate(date): String {
      return new Date(date).toLocaleDateString('en-us', { year:"numeric", month:"short", day:"numeric"})
    }

    function convertBucketType(fileType): String {
      switch (fileType) {
        case 1:
          return 'Request for upload to Apillon storage was generated.';
        case 2:
          return 'File is uploaded to Apillon central server.';
        case 3:
          return 'File is transferred to the IPFS node.';
        case 4:
          return 'File is replicated to different IPFS nodes through Crust Network.';
        default:
          return '';
      }
    }

    function truncatedText(text): String {
      if (text && text.length > 40) {
        return text.substring(0, 40) + '...';
      }
      return text;
    };

    function openUpload() {
      open.value = true;
    }

    function uploadFile() {
      file = this.$refs.file.files[0];
      console.log(file)
    }

    async function end(sessionId) {
      await axios.post(`https://api.apillon.io/storage/buckets/${props.uuid.id}/upload/${sessionId}/end`,
        {},
        {
          'headers': {
            'Content-Type': 'application/json',
            'Authorization': `Basic ${btoa('f63ffc52-6dfd-414c-8ab9-6cc7d4a47551:W&2R2#m0i53R')}`
          }
        })
        .then(async () =>  {
          open.value = false;
          await fetchBucketsConetent();
        })
        .catch(e => {
          console.log(e);
        })
    }

    async function uploadSubmitedFile(url, sessionId) {
      await axios.put(url,
        {
          'headers': {
            'Content-Type': 'multipart/form-data',
          }
        })
        .then(async () =>  {
          await end(sessionId);
        })
        .catch(e => {
          console.log(e);
        })
    }

    async function submitFile() {
      const formData = new FormData();
      formData.append('file', file);

      await axios.post(`https://api.apillon.io/storage/buckets/${props.uuid.id}/upload`,
        {
          files: [
            {
              fileName: file.name,
              contentType: file.type,
              path: ""
            }
          ]
        },
        {
          'headers': {
            'Content-Type': 'application/json',
            'Authorization': `Basic ${btoa('f63ffc52-6dfd-414c-8ab9-6cc7d4a47551:W&2R2#m0i53R')}`
          }
        })
        .then(async (res) =>  {
          await uploadSubmitedFile(res.data.data.files[0].url, res.data.data.sessionUuid)
        })
        .catch(e => {
          console.log(e);
        })
    }

    

    return {
      filteredBucketsContent,
      open,
      fetchBucketsConetent,
      handleSearch,
      convertSize,
      convertTimeDate,
      convertBucketType,
      truncatedText,
      openUpload,
      uploadFile,
      submitFile,
    }
  }
})
</script>

<style>

</style>