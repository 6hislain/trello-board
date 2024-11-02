<template>
  <div>
    <textarea
      v-model="title"
      @keydown.tab="createTask"
      @keyup.enter="createTask"
      class="focus:bg-white focus:shadow resize-none rounded w-full border-none"
      :class="{ 'h-7': !focused, 'h-20': focused }"
      style="outline: none !important"
      @focus="focused = true"
      @blur="focued = false"
      :placeholder="!focused ? '+ Add a Card' : 'Enter a title for this card'"
    />
  </div>
</template>
<script setup lang="ts">
import { nanoid } from "nanoid";
import type { Task } from "@/types";

const title = ref("");
const focused = ref(false);
const emit = defineEmits<{ (e: "add", payload: Task): void }>();

function createTask(e: Event) {
  if (title.value.trim()) {
    e.preventDefault();
    emit("add", {
      title: title.value.trim(),
      createdAt: new Date(),
      id: nanoid(),
    } as Task);
  }
  title.value = "";
}
</script>
