<template>
  <!-- When the mobile menu is open, add `overflow-hidden` to the `body` element to prevent double scrollbars -->
  <Popover as="template" v-slot="{ open }">
    <header
      :class="[
        open ? 'fixed inset-0 z-40 overflow-y-auto' : '',
        'bg-white shadow-sm lg:static lg:overflow-y-visible',
      ]"
    >
      <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div
          class="relative flex justify-between xl:grid xl:grid-cols-12 lg:gap-8"
        >
          <div
            class="flex md:absolute md:left-0 md:inset-y-0 lg:static xl:col-span-2"
          >
            <div class="flex-shrink-0 flex items-center">
              <a href="#">
                <div class="text-xl">My Notes</div>
              </a>
            </div>
          </div>
          <div class="min-w-0 flex-1 md:px-8 lg:px-0 xl:col-span-6">
            <div
              class="flex items-center px-6 py-4 md:max-w-3xl md:mx-auto lg:max-w-none lg:mx-0 xl:px-0"
            >
              <div class="w-full">
                <label for="search" class="sr-only">Search</label>
                <div class="relative">
                  <div
                    class="pointer-events-none absolute inset-y-0 left-0 pl-3 flex items-center"
                  >
                    <Icon class="h-5 w-5 text-gray-400" name="uil:search" />
                  </div>
                  <input
                    v-model="search"
                    @keyup="
                      refetch(`http://redcomm.shrall.tech/api/note?page1`, search)
                    "
                    id="search"
                    name="search"
                    class="block w-full bg-white border border-gray-300 rounded-md py-2 pl-10 pr-3 text-sm placeholder-gray-500 focus:outline-none focus:text-gray-900 focus:placeholder-gray-400 focus:ring-1 focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
                    placeholder="Search"
                    type="search"
                  />
                </div>
              </div>
            </div>
          </div>
          <div class="flex items-center justify-end col-span-4">
            <div
              @click="showNoteForm = true"
              class="cursor-pointer ml-6 inline-flex items-center px-4 py-2 border border-transparent text-sm font-medium rounded-md shadow-sm text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
            >
              <Icon name="uil:plus" class="w-5 h-5 bg-indigo-600" />
            </div>
          </div>
        </div>
      </div>
    </header>
  </Popover>
  <div class="flex flex-col flex-1">
    <main class="flex-1">
      <div class="p-6">
        <div class="grid grid-cols-1 gap-4 sm:grid-cols-2">
          <div
            v-for="note in notes.data.api_results.data"
            :key="note.id"
            class="relative rounded-lg border border-gray-300 bg-white px-6 py-5 shadow-sm flex items-center space-x-3 hover:border-gray-400 focus-within:ring-2 focus-within:ring-offset-2 focus-within:ring-indigo-500"
          >
            <div
              @click="editNote(note.id, note.title, note.content)"
              class="flex-1 min-w-0 cursor-pointer"
            >
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
            <Icon
              name="fa:trash"
              @click="submitDeleteNote(note.id)"
              class="cursor-pointer text-red-500 hover:opacity-80 absolute right-6"
            />
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
      </div>
    </main>
  </div>
  <TransitionRoot as="template" :show="showNoteForm">
    <Dialog
      as="div"
      class="fixed z-10 inset-0 overflow-y-auto"
      @close="showNoteForm = false"
    >
      <div
        class="flex items-center justify-center min-h-screen pt-4 px-4 pb-20 text-center sm:block sm:p-0"
      >
        <TransitionChild
          as="template"
          enter="ease-out duration-300"
          enter-from="opacity-0"
          enter-to="opacity-100"
          leave="ease-in duration-200"
          leave-from="opacity-100"
          leave-to="opacity-0"
        >
          <DialogOverlay
            class="fixed inset-0 bg-gray-500 bg-opacity-75 transition-opacity"
          />
        </TransitionChild>

        <!-- This element is to trick the browser into centering the modal contents. -->
        <span
          class="hidden sm:inline-block sm:align-middle sm:h-screen"
          aria-hidden="true"
          >&#8203;</span
        >
        <TransitionChild
          as="template"
          enter="ease-out duration-300"
          enter-from="opacity-0 translate-y-4 sm:translate-y-0 sm:scale-95"
          enter-to="opacity-100 translate-y-0 sm:scale-100"
          leave="ease-in duration-200"
          leave-from="opacity-100 translate-y-0 sm:scale-100"
          leave-to="opacity-0 translate-y-4 sm:translate-y-0 sm:scale-95"
        >
          <div
            class="relative inline-block align-bottom bg-white rounded-lg px-4 pt-5 pb-4 text-left overflow-hidden shadow-xl transform transition-all sm:my-8 sm:align-middle sm:max-w-xl sm:w-full sm:p-6"
          >
            <form class="space-y-8 divide-y divide-gray-200">
              <div class="space-y-8 divide-y divide-gray-200">
                <div>
                  <div>
                    <h3 class="text-lg leading-6 font-medium text-gray-900">
                      {{ isEditing ? "Edit Note" : "Add New Note" }}
                    </h3>
                    <p class="mt-1 text-sm text-gray-500">
                      Please make sure all datas are correct.
                    </p>
                  </div>
                  <hr />
                  <div
                    class="max-w-7xl mt-2 grid grid-cols-1 mx-auto mb-8 gap-x-4"
                  >
                    <div class="flex flex-col col-span-1 h-full gap-y-2">
                      <div class="sm:col-span-6">
                        <label
                          for="title"
                          class="block text-sm font-medium text-gray-700"
                        >
                          Title
                        </label>
                        <div class="mt-1">
                          <input
                            type="text"
                            v-model="title"
                            id="title"
                            name="title"
                            class="shadow-sm focus:ring-black border focus:border-black block w-full sm:text-sm border-gray-300 rounded-md"
                          />
                        </div>
                      </div>
                      <div class="sm:col-span-6">
                        <label
                          for="content"
                          class="block text-sm font-medium text-gray-700"
                        >
                          Content
                        </label>
                        <div class="mt-1">
                          <textarea
                            v-model="content"
                            class="shadow-sm focus:ring-black border focus:border-black block w-full sm:text-sm border-gray-300 rounded-md"
                            name="content"
                            id="content"
                            cols="30"
                            rows="10"
                          ></textarea>
                        </div>
                      </div>
                    </div>
                  </div>
                </div>
              </div>

              <div class="pt-5">
                <div class="flex justify-end">
                  <button
                    type="button"
                    @click="submitDeleteNote(selectedID)"
                    class="bg-white py-2 px-4 border border-gray-300 rounded-md shadow-sm text-sm font-medium text-gray-700 hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-black"
                  >
                    Delete
                  </button>
                  <button
                    type="button"
                    @click="isEditing ? submitEditNote() : submitAddNote()"
                    class="ml-3 inline-flex justify-center py-2 px-4 border border-transparent shadow-sm text-sm font-medium rounded-md text-white bg-black hover:opacity-90 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-black"
                  >
                    {{ "Save" }}
                  </button>
                </div>
              </div>
            </form>
          </div>
        </TransitionChild>
      </div>
    </Dialog>
  </TransitionRoot>
</template>

<script>
import {
  Menu,
  MenuButton,
  MenuItem,
  MenuItems,
  Popover,
  PopoverButton,
  PopoverPanel,
  Dialog,
  DialogOverlay,
  TransitionChild,
  TransitionRoot,
} from "@headlessui/vue";

export default {
  components: {
    Menu,
    MenuButton,
    MenuItem,
    MenuItems,
    Popover,
    PopoverButton,
    PopoverPanel,
    Dialog,
    DialogOverlay,
    TransitionChild,
    TransitionRoot,
  },
  setup() {
    const link = ref("http://redcomm.shrall.tech/api/note?page1");
    const { pending, data: notes } = useLazyAsyncData("notes", () =>
      $fetch(`${link.value}`)
    );

    const refresh = () => refreshNuxtData("notes");

    function refetch(linkURL, search) {
      if (search) {
        link.value = `${linkURL}&search=${search}`;
      } else {
        link.value = linkURL;
      }
      refresh();
    }
    return {
      link,
      notes,
      refresh,
      refetch,
    };
  },
  data() {
    return {
      showNoteForm: false,
      isEditing: false,
      search: null,
      title: null,
      content: null,
      selectedID: null,
    };
  },
  methods: {
    resetVariables() {
      this.title = null;
      this.content = null;
      this.selectedID = null;
      this.showNoteForm = false;
      this.isEditing = false;
      this.refresh();
    },
    editNote(id, title, content) {
      this.selectedID = id;
      this.title = title;
      this.content = content;
      this.showNoteForm = true;
      this.isEditing = true;
    },
    async submitAddNote() {
      await $fetch("http://redcomm.shrall.tech/api/note", {
        method: "POST",
        body: {
          title: this.title,
          content: this.content,
        },
      }).then(() => {
        this.resetVariables();
      });
    },
    async submitEditNote() {
      await $fetch(`http://redcomm.shrall.tech/api/note/${this.id}`, {
        method: "PUT",
        body: {
          title: this.title,
          content: this.content,
        },
      }).then(() => {
        this.resetVariables();
      });
    },
    async submitDeleteNote(id) {
      await $fetch(`http://redcomm.shrall.tech/api/note/${id}`, {
        method: "DELETE",
      }).then(() => {
        this.resetVariables();
      });
    },
  },
};
</script>
