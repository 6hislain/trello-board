<template>
  <div class="flex items-start overflow-x-auto gap-4">
    <draggable
      class="flex items-start gap-4"
      handle=".drag-handle"
      v-model="columns"
      :animation="150"
      group="columns"
      item-key="id"
    >
      <template #item="{ element: column }: { element: Column }">
        <div class="column bg-gray-200 p-5 rounded min-w-[250px]">
          <header class="font-bold mb-4">
            <DragHandle />
            <input
              class="title-input bg-transparent focus:bg-white rounded px-1 w-4/5"
              @keyup.enter="($event.target as HTMLInputElement).blur()"
              @keydown.backspace="
                column.title === ''
                  ? (columns = columns.filter((c) => c.id !== column.id))
                  : null
              "
              v-model="column.title"
              type="text"
            />
          </header>
          <draggable
            :group="{ name: 'tasks', pull: alt ? 'clone' : true }"
            v-model="column.tasks"
            handle=".drag-handle"
            :animation="150"
            item-key="id"
          >
            <template #item="{ element: task }: { element: Task }">
              <div>
                <TrelloBoardTask
                  :task="task"
                  @delete="
                    column.tasks = column.tasks.filter((t) => t.id !== $event)
                  "
                />
              </div>
            </template>
          </draggable>
          <footer>
            <NewTask @add="column.tasks.push($event)" />
          </footer>
        </div>
      </template>
    </draggable>
    <button
      @click="createColumn"
      class="bg-gray-200 whitespace-nowrap p-2 rounded opacity-50"
    >
      + Add Another Column
    </button>
  </div>
</template>
<script setup lang="ts">
import { nanoid } from "nanoid";
import draggable from "vuedraggable";
import type { Column, Task } from "@/types";

const alt = useKeyModifier("Alt");

const columns = useLocalStorage<Column[]>("trello-board", [
  {
    id: nanoid(),
    title: "backlog",
    tasks: [
      {
        id: nanoid(),
        title: "create marketing landing page",
        createdAt: new Date(),
      },
      {
        id: nanoid(),
        title: "develop cool new features",
        createdAt: new Date(),
      },
      {
        id: nanoid(),
        title: "fix page nav bug",
        createdAt: new Date(),
      },
    ],
  },
  { id: nanoid(), title: "selected for dev", tasks: [] },
  { id: nanoid(), title: "in progress", tasks: [] },
  { id: nanoid(), title: "complete", tasks: [] },
]);

function createColumn() {
  const column: Column = { id: nanoid(), title: "", tasks: [] };
  columns.value.push(column);

  nextTick(() => {
    (
      document.querySelector(
        ".column:last-of-type .title-input"
      ) as HTMLInputElement
    ).focus();
  });
}
</script>
