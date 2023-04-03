<template>
  <div class="grid grid-cols-1 gap-4 sm:grid-cols-2">
    <div
      v-for="note in notes.data.api_results.data.slice(prevCount, nextCount)"
      :key="note.id"
      class="relative rounded-lg border border-gray-300 bg-white px-6 py-5 shadow-sm flex items-center space-x-3 hover:border-gray-400 focus-within:ring-2 focus-within:ring-offset-2 focus-within:ring-indigo-500"
    >
      <div class="flex-1 min-w-0 cursor-pointer">
        <div class="focus:outline-none">
          <span class="absolute inset-0" aria-hidden="true" />
          <p class="text-sm font-medium text-gray-900">
            {{ note.title }}
          </p>
          <p class="text-sm text-gray-500 line-clamp-4">
            {{ note.content }}
          </p>
        </div>
      </div>
    </div>
  </div>
  <Pagination
    @change="refetch"
    :pageLinks="notes.data.api_results.links"
    :from="notes.data.api_results.from"
    :to="notes.data.api_results.to"
    :total="notes.data.api_results.total"
    :nextPageURL="notes.data.api_results.next_page_url"
    :prevPageURL="notes.data.api_results.prev_page_url"
  />
</template>

<script setup>
const link = ref("http://redcommnotes.test/api/note?page1");
const { pending, data: notes } = useLazyAsyncData("notes", () =>
  $fetch(`${link.value}`)
);

const refresh = () => refreshNuxtData("notes");

function refetch(linkURL) {
  link.value = linkURL;
  refresh();
}
</script>
<script>
export default {
  data() {
    return {
      prevCount: 0,
      nextCount: 10,
    };
  },
  methods: {
    nextPage() {
      console.log(this.notes);
    },
    prevPage() {},
  },
};
</script>
