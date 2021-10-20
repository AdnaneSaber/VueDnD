<template>
  <div
    class="drop-zone"
    @drop="onDrop($event, 1)"
    @dragenter.prevent
    @dragover.prevent
  >
    <div
      v-for="item in getList(1)"
      :key="item.id"
      :id="item.id"
      class="drag-el"
      draggable="true"
      @dragstart="startDrag($event, item)"
    >
      {{ item.title }}
    </div>
  </div>
  <div
    class="drop-zone"
    @drop="onDrop($event, 2)"
    @dragenter.prevent
    @dragover.prevent
  >
    <div
      v-for="item in getList(2)"
      :key="item.id"
      :id="item.id"
      class="drag-el"
      draggable="true"
      @dragstart="startDrag($event, item)"
    >
      {{ item.title }}
    </div>
  </div>
  <button @click="createEl(2)">list</button>
  <div id="hh">
    <div class="resize-drag">Resize from any edge or corner</div>
  </div>
</template>

<script>
import { ref } from "vue";
import { uuid } from "vue-uuid";
import interact from "https://cdn.interactjs.io/v1.10.11/interactjs/index.js";
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
    interact(".resize-drag")
      .resizable({
        // resize from all edges and corners
        edges: { left: true, right: true, bottom: true, top: true },

        listeners: {
          move(event) {
            var target = event.target;
            var x = parseFloat(target.getAttribute("data-x")) || 0;
            var y = parseFloat(target.getAttribute("data-y")) || 0;

            // update the element's style
            target.style.width = event.rect.width + "px";
            target.style.height = event.rect.height + "px";

            // translate when resizing from top or left edges
            x += event.deltaRect.left;
            y += event.deltaRect.top;

            target.style.transform = "translate(" + x + "px," + y + "px)";

            target.setAttribute("data-x", x);
            target.setAttribute("data-y", y);
            target.textContent =
              Math.round(event.rect.width) +
              "\u00D7" +
              Math.round(event.rect.height);
          },
        },
        modifiers: [
          // keep the edges inside the parent
          interact.modifiers.restrictEdges({
            outer: "parent",
          }),

          // minimum size
          interact.modifiers.restrictSize({
            min: { width: 100, height: 50 },
          }),
        ],

        inertia: true,
      })
      .on("dragmove", function (event) {
        x += event.dx;
        y += event.dy;

        event.target.style.transform = "translate(" + x + "px, " + y + "px)";
      })
      .draggable({
        listeners: { move: window.dragMoveListener },
        inertia: true,
        modifiers: [
          interact.modifiers.restrictRect({
            restriction: "parent",
            endOnly: true,
          }),
        ],
      });
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

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.drop-zone {
  width: 50%;
  margin: 50px auto;
  background-color: #ecf0f1;
  padding: 10px;
  min-height: 10px;
}
.drag-el {
  background-color: #3498db;
  color: white;
  padding: 5px;
  margin-bottom: 10px;
}
.drag-el:last-child {
  margin-bottom: 0;
}
.resize-drag {
  width: 120px;
  border-radius: 8px;
  padding: 20px;
  margin: 1rem;
  background-color: #29e;
  color: white;
  font-size: 20px;
  font-family: sans-serif;
  height: 50px;
  touch-action: none;

  /* This makes things *much* easier */
  box-sizing: border-box;
}
#hh {
  height: 100vh;
  width: 100vw;
}
</style>
