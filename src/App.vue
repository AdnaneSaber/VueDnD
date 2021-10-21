<template>
  <div class="c0mdment">
    <div class="line" v-for="dropZone in dropZones" :key="dropZone.id">
      <div
        :class="dropZone.class"
        :id="dropZone.id"
        @drop="onDrop($event, dropZone.id)"
        @dragenter.prevent
        @dragover.prevent
      >
        <div
          v-for="item in getList(dropZone.id)"
          :key="item.id"
          :id="item.id"
          class="drag-el"
          draggable="true"
          @dragstart="startDrag($event, item)"
        >
          {{ item.title }}
        </div>
      </div>
    </div>
    <button @click="addLine">Create a dropZone</button>
    <button @click="createEl">Create a node</button>
  </div>
  <div class="container c0mment">
    <select name="zoom" id="zommSelection" v-model="zoom" @change="createMms">
      <option value="1" selected="true">x1</option>
      <option value="2">x2</option>
      <option value="10">x10</option>
    </select>
    <div class="timeLineFirstLineContainer">
      <button class="addTimeLineLineButton" @click="addLine">+</button>
      <div id="ruler"></div>
    </div>
    <div class="timeLineLines">
      <div class="line">
        <div
          class="img"
          style="background-image: url('./src/assets/timeLineImage.jpg')"
        ></div>
      </div>
    </div>
  </div>
  <!-- <LineVue /> -->
</template>

<script>
import { ref } from "vue";
import { uuid } from "vue-uuid";
// import $ from "jquery";
// import "jquery-ui-dist/jquery-ui";
import "https://cdnjs.cloudflare.com/ajax/libs/jquery/3.6.0/jquery.min.js";
import "https://cdnjs.cloudflare.com/ajax/libs/jqueryui/1.12.1/jquery-ui.min.js";
import LineVue from "./components/Line.vue";
let jQuery = $;
// import interact from "https://cdn.interactjs.io/v1.10.11/interactjs/index.js";
export default {
  components: {
    LineVue,
  },
  data() {
    return {
      zoom: 1,
      dropZones: [
        {
          id: "5",
          class: "drop-zone",
        },
      ],
    };
  },
  methods: {
    Dropped(event, ui) {
      $(".drag-el").each(function () {
        //var p = $(this).position();
      });
      refresh();
    },
    onResize() {
      this.createMms();
    },
    addLine() {
      this.dropZones.push({
        id: uuid.v4(),
        class: "drop-zone",
      });
      $(".drop-zone").sortable({
        update: function (event, ui) {
          // Dropped();
        },
      });
      $(".drag-zone").draggable({
        drag: function (event, ui) {
          var snapTolerance = $(this).draggable("option", "snapTolerance");
          var topRemainder = ui.position.top % 50;
          var leftRemainder = ui.position.left % 50;

          if (topRemainder <= snapTolerance) {
            ui.position.top = ui.position.top - topRemainder;
          }

          if (leftRemainder <= snapTolerance) {
            ui.position.left = ui.position.left - leftRemainder;
          }
        },
      });
    },
    rulerEnumeration() {
      let rulerCounter = 0;
      $("#ruler")
        .children(".mms")
        .each(function () {
          if (rulerCounter % 5 === 0) {
            $(this).attr("data-number", rulerCounter / 5);
            $(this).addClass("moduled");
          }
          rulerCounter += 1;
        });
    },
    createMms() {
      $("#ruler").empty();
      let number_of_mms = Math.round($("#ruler").width() / 5);
      if ($("#ruler .mms").length < number_of_mms) {
        for (let i = 0; i < number_of_mms; i++) {
          $("#ruler").append(`<div class='mms zx${this.zoom}'></div>`);
        }
      }
      this.rulerEnumeration();
    },
  },
  setup() {
    const items = ref([]);
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
    const createEl = () => {
      const firstLineID = $(".line:first-child .drop-zone").attr("id");
      items.value.push({
        id: uuid.v4(),
        title: `Item ${uuid.v4()}`,
        list: firstLineID,
      });
    };
    return { getList, onDrop, createEl, uuid, startDrag };
  },
  mounted() {
    window.addEventListener("resize", this.onResize);
    this.createMms();
    this.rulerEnumeration();
    // $("#1")
    $(".drag-el").addClass("hh");
  },
  beforeDestroy() {
    window.removeEventListener("resize", this.onResize);
  },
};
</script>

<style lang="scss">
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
.c0mment {
  display: none !important;
}
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
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
}
.drag-el {
  background-color: #3498db;
  color: white;
  padding: 5px;
  margin: 10px;
  max-width: calc(100% / 4);
}
.drag-el:last-child {
  margin-bottom: 0;
}

.container {
  /* background-color: #cccccc; */
  width: 100%;
  height: 100vw;
  display: flex;
  flex-direction: column;
  overflow-x: auto;
  background-color: #333;
  &::-webkit-scrollbar-track {
    background: #f1f1f1;
  }
  &::-webkit-scrollbar-thumb {
    background: #888;
  }
  &::-webkit-scrollbar-thumb:hover {
    background: #555;
  }
  &::-webkit-scrollbar {
    height: 8px;
  }
  .timeLineFirstLineContainer {
    display: flex;
    width: 100%;
    max-height: 70px;
    height: 100%;
    button.addTimeLineLineButton {
      min-width: 150px;
      background-color: #1555d6;
      color: #fff;
      font-size: 2em;
      border: none;
      cursor: pointer;
      &:hover {
        filter: brightness(1.1);
        &:active {
          filter: brightness(0.8);
        }
      }
    }
    #ruler {
      width: 100%;
      height: 100%;
      display: flex;
      flex-wrap: nowrap;
      padding: 0 0.6em;

      .mms {
        width: 5px;
        min-width: 5px;
        background-color: transparent;
        height: 15px;
        border-left: 1px solid#ffffff50;
        position: relative;
        &.zx2 {
          min-width: 10px;
        }
        &.zx10 {
          min-width: 100px;
        }
        &.moduled {
          height: 25px;

          &::before {
            display: block;
          }
        }
        &::before {
          content: attr(data-number);
          top: 111%;
          transform: translateX(-50%);
          position: absolute;
          color: #fff;
          font-size: 10px;
          display: none;
        }
      }
    }
  }
  .timeLineLines {
    .line {
      padding-left: 150px;
      .img {
        width: 100%;
        height: 70px;
        background-repeat: repeat-x;
        background-size: contain;
      }
    }
  }
}
</style>
