<template>
  <div
    class="drop-zone"
    @drop="onDrop($event, 3)"
    @dragenter.prevent
    @dragover.prevent
  >
    <div
      v-for="item in getList(3)"
      :key="item.id"
      :id="item.id"
      class="drag-el"
      draggable="true"
      @dragstart="startDrag($event, item)"
    >
      {{ item.title }}
    </div>
  </div>
</template>

<script>
import { ref } from "vue";
import { uuid } from "vue-uuid";
export default {
  setup() {
    const items = ref([
      { id: 0, title: "Item A", list: 1 },
      { id: 1, title: "Item B", list: 1 },
      { id: 2, title: "Item C", list: 2 },
    ]);
    const getList = (list) => {
      return items.value.filter((item) => item.list == list);
    };
    const startDrag = (event, item) => {
      event.dataTransfer.dropEffect = "move";
      event.dataTransfer.effectAllowed = "move";
      event.dataTransfer.setData("itemID", item.id);
    };
    const onDrop = (event, list) => {
      const itemID = event.dataTransfer.getData("itemID");
      const item = items.value.find((item) => item.id == itemID);
      item.list = list;
    };
    const createEl = (list) => {
      items.value.push({ id: uuid.v4(), title: `Item ${uuid.v4()}`, list });
    };
    return { getList, onDrop, createEl, uuid, startDrag };
  },
};
</script>

<style></style>
