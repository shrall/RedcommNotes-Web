<!-- This example requires Tailwind CSS v2.0+ -->
<template>
  <div
    class="bg-white px-4 py-3 flex items-center justify-between border-t border-gray-200 sm:px-6"
  >
    <div class="flex-1 flex justify-between sm:hidden">
      <button
        @click="$emit('change', prevPageURL)"
        :disabled="!prevPageURL"
        class="cursor-pointer disabled:opacity-50 relative inline-flex items-center px-4 py-2 border border-gray-300 text-sm font-medium rounded-md text-gray-700 bg-white hover:bg-gray-50"
      >
        Previous
      </button>
      <button
        @click="$emit('change', nextPageURL)"
        :disabled="!nextPageURL"
        class="cursor-pointer disabled:opacity-50 ml-3 relative inline-flex items-center px-4 py-2 border border-gray-300 text-sm font-medium rounded-md text-gray-700 bg-white hover:bg-gray-50"
      >
        Next
      </button>
    </div>
    <div class="hidden sm:flex-1 sm:flex sm:items-center sm:justify-between">
      <div>
        <p class="text-sm text-gray-700">
          Showing
          {{ " " }}
          <span class="font-medium">{{ from }}</span>
          {{ " " }}
          to
          {{ " " }}
          <span class="font-medium">{{ to }}</span>
          {{ " " }}
          of
          {{ " " }}
          <span class="font-medium">{{ total }}</span>
          {{ " " }}
          results
        </p>
      </div>
      <div>
        <nav
          class="relative z-0 inline-flex rounded-md shadow-sm -space-x-px"
          aria-label="Pagination"
        >
          <button
            v-for="(link, index) in pageLinks"
            :key="link"
            :disabled="!link.url"
            @click="$emit('change', link.url)"
            :class="[
              link.active
                ? 'z-10 bg-indigo-50 border-indigo-500 text-indigo-600 relative inline-flex items-center px-4 py-2 border text-sm font-medium'
                : 'disabled:opacity-50 bg-white border-gray-300 text-gray-500 hover:bg-gray-50 relative inline-flex items-center px-4 py-2 border text-sm font-medium',
            ]"
          >
            <Icon name="uil:arrow-left" class="w-5 h-5" v-if="index == 0" />
            <Icon
              name="uil:arrow-right"
              class="w-5 h-5"
              v-else-if="index == pageLinks.length - 1"
            />
            <span v-else>{{ link.label }}</span>
          </button>
        </nav>
      </div>
    </div>
  </div>
</template>

<script setup>
defineProps({
  pageLinks: Array,
  from: Number,
  to: Number,
  total: Number,
  nextPageURL: String,
  prevPageURL: String,
});
defineEmits(["change"]);
</script>
