<script setup lang="ts">
defineProps<{
  show: boolean;
  url: string[];
}>();

const emit = defineEmits(["close"]);

function closeModal() {
  emit("close");
}
</script>

<template>
  <div
    v-if="show"
    class="fixed inset-0 z-50 bg-black/70 flex items-center justify-center p-4"
    @click.self="closeModal()"
  >
    <div class="relative w-full max-w-6xl h-[80vh]">
      <button
        class="absolute top-0 right-[-5px] z-10 p-2 bg-black/50 rounded-full text-white hover:bg-opacity-70 transition-colors"
        @click="closeModal()"
      >
        <svg
          xmlns="http://www.w3.org/2000/svg"
          class="h-6 w-6"
          fill="none"
          viewBox="0 0 24 24"
          stroke="currentColor"
        >
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            stroke-width="2"
            d="M6 18L18 6M6 6l12 12"
          />
        </svg>
      </button>
      <div class="relative w-full max-w-5xl h-[80vh] bg-white rounded-lg shadow-xl overflow-hidden">
        <iframe
          v-if="url"
          :src="url"
          class="w-full h-full border-none"
        />
        <div
          v-else
          class="w-full h-full flex items-center justify-center text-red-500"
        >
          Failed to load PDF
        </div>
      </div>
    </div>
  </div>
</template>
