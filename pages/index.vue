<template>
  <div class="flex items-center justify-center h-screen">
    <div class="w-full max-w-md">
      <p class="font-bold text-4xl mb-4 text-center">Name List Statistics</p>
      <hr class="mb-4">
      <p class="font-medium text-xl mb-4 text-center">Find out the number of masculine and feminine names in your list.</p>
      <p class="text-md mb-4 text-center">Paste a list of names below, separating each name by comma:</p>
      <textarea
        v-model="namesList"
        placeholder="Nathan, John, Mary, Jane..."
        class="border border-gray-400 rounded-lg p-2 w-full"
      ></textarea>
      <span v-if="namesList.length" class="text-sm text-blue-600 cursor-pointer" @click="namesList = []">Clear list</span>
      <button
        class="bg-blue-500 text-white py-2 px-4 rounded-lg mt-2 w-full hover:bg-blue-600"
        :class="namesList.length ? '' : 'cursor-not-allowed opacity-50'"
        @click="countNames"
      >
        Count Names
      </button>
      <div v-if="loading" class="flex justify-center">
        <svg
          class="animate-spin h-16 w-16 text-blue-500"
          xmlns="http://www.w3.org/2000/svg"
          fill="none"
          viewBox="0 0 24 24"
        >
          <circle
            class="opacity-25"
            cx="12"
            cy="12"
            r="10"
            stroke="currentColor"
            stroke-width="4"
          />
          <path
            class="opacity-75"
            fill="currentColor"
            d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"
          />
        </svg>
      </div>
      <div v-else class="flex mt-4 gap-4 text-center">
        <div class="w-1/2 p-4 bg-gray-100 rounded-lg shadow-md">
          <p class="font-bold text-lg">Feminine Names:</p>
          <p class="text-2xl">{{ feminineCount }}</p>
        </div>
        <div class="w-1/2 p-4 bg-gray-100 rounded-lg shadow-md text-center">
          <p class="font-bold text-lg">Masculine Names:</p>
          <p class="text-2xl">{{ masculineCount }}</p>
        </div>
      </div>
      <div v-if="notFound.length > 0">
        <p class="mt-4 font-bold text-center text-lg">Names Not Found: {{ notFound.length }}</p>
        <ul v-if="notFound.length > 0" class="mt-4 rounded-lg shadow-md bg-gray-100 p-4 h-64 overflow-auto">
          <li v-for="name in notFound" :key="name.index" class="py-2 font-medium capitalize">{{ name }}</li>
        </ul>
      </div>
    </div>
  </div>
</template>
<script>

import nameData from "../names/names.json";
export default {
  data() {
    return {
      namesList: "",
      masculineCount: 0,
      feminineCount: 0,
      loading: false,
      notFound: []
    };
  },
  methods: {
    async countNames() {
      if (!this.namesList.length) return;
      this.loading = true;
      this.notFound = [];
      this.feminineCount = 0;
      this.masculineCount = 0;

      const names = this.namesList.split(",").map(name => name.trim().toLowerCase());

      const masculineNames = nameData.names.masculineNames.map(name => name.toLowerCase());
      const feminineNames = nameData.names.feminineNames.map(name => name.toLowerCase());


      await new Promise(resolve => {
        for (const name of names) {
          if (feminineNames.includes(name)) {
            this.feminineCount++;
          } else if (masculineNames.includes(name)) {
            this.masculineCount++;
          } else {
            this.notFound.push(name);
          }
        }

        resolve();
      });

      this.loading = false;
    }
  }
};
</script>
