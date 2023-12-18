<template>
  <div>
    <div class="pb-8 min-h-[40px]" style="display: flex; flex-flow; row; justify-content: space-between; align-items: center; gap: 32px;">
      <div style="display: flex; flex-flow; row; justify-content: space-between; align-items: center; gap: 32px;">
        <div class="myInputWrap">
          <input
            v-model="filter"
            type="text"
            class="myInput"
            placeholder="Search files"
            @change="filterByName"
          />
        </div>
        <div>
        </div>
      </div>
      <div style="display: flex; flex-flow; row; justify-content: space-between; align-items: center; gap: 32px;">
      </div>
    </div>
    <table class="min-w-full">
      <colgroup>
        <col style="width: 40px; min-width: 40px;">
        <col>
        <col>
        <col>
        <col>
        <col>
        <col>
      </colgroup>
      <thead>
        <tr>
          <th colspan="1" rowspan="1"></th>
          <th colspan="1" rowspan="1">Bucket name</th>
          <th colspan="1" rowspan="1">Bucket type</th>
          <th colspan="1" rowspan="1">Bucket Uuid</th>
          <th colspan="1" rowspan="1">Storage usage</th>
          <th colspan="1" rowspan="1">Description</th>
          <th colspan="1" rowspan="1"></th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(data, i) in filteredBuckets" :key="i">
          <td>
            <input
              v-model="data.selected"
              type="checkbox"/>
          </td>
          <td>{{ data.name }}</td>
          <td>{{ convertBucketType(data.bucketType) }}</td>
          <td>{{ data.bucketUuid }}</td>
          <td>{{ convertSize(data.size) }}</td>
          <td>{{ data.description }}</td>
          <td>
            <button>
              <svg
                xmlns="http://www.w3.org/2000/svg"
                fill="none"
                viewBox="0 0 24 24"
                stroke-width="1.5"
                stroke="currentColor"
                class="w-6 h-6"
              >
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  d="M12 6.75a.75.75 0 110-1.5.75.75 0 010 1.5zM12 12.75a.75.75 0 110-1.5.75.75 0 010 1.5zM12 18.75a.75.75 0 110-1.5.75.75 0 010 1.5z"
                />
              </svg>
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, computed } from 'vue';
import axios from 'axios';

export default defineComponent({
  setup() {
    const filter = ref<string>('');

    interface BucketData {
      selected: boolean
      createTime: string,
      updateTime: string,
      bucketUuid: string,
      bucketType: number,
      name: string,
      description: string,
      size: number
    }
    const tableData: BucketData[] =
      [
        {
          selected: false,
          createTime: "2022-12-06T12:03:32.000Z",
          updateTime: "2023-10-09T11:42:23.000Z",
          bucketUuid: "cee9f151-a371-495b-acd2-4362fbb87780",
          bucketType: 1,
          name: "Storage",
          description: "",
          size: 3215839730,
        },
        {
          selected: false,
          createTime: "2022-12-06T12:03:32.000Z",
          updateTime: "2023-10-09T11:42:23.000Z",
          bucketUuid: "cee9f151-a371-495b-acd2-4362fbb87780",
          bucketType: 2,
          name: "Storage bucket",
          description: "",
          size: 32100,
        },
        {
          selected: false,
          createTime: "2022-12-06T12:03:32.000Z",
          updateTime: "2023-10-09T11:42:23.000Z",
          bucketUuid: "cee9f151-a371-495b-acd2-4362fbb87780",
          bucketType: 3,
          name: "Storage bucket",
          description: "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa",
          size: 3215839730,
        },
      ]

    const filteredBuckets = computed(() => {
      if (!filter.value) {
        return tableData;
      } else {
        return tableData.filter(item =>
          item.name.toLowerCase().includes(filter.value.toLowerCase())
        );
      }
    });

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
        case (0 < size && size < 1024):
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

    return {
      filter,
      tableData,
      convertBucketType,
      convertSize,
      filteredBuckets,
    }
  }
})
</script>

<style>

</style>