<template>
    <div class="board"  tabIndex="1">
        <div v-for="(r_item, index1) in board.cells" :key="index1">
            <cell v-for="(c_item, index2) in r_item" :key="index2"></cell>
        </div>
        <tile-view v-for="(tile, index3) in tiles" :key="'title' + index3" :tile="tile"/>

        <game-end-overlay :board="board" :onrestart="onRestart"></game-end-overlay>
    </div>
</template>

<script>
import Cell from "./Cell.vue";
import TileView from "./TileView.vue";
import GameEndOverlay from "./GameEndOverlay.vue";
import { Board } from "../board";
export default {
  data() {
    return {
      board: new Board()
    };
  },
  mounted() {
    var self = this;
    window.addEventListener("keydown", this.handleKeyDown.bind(this));

    var options = {isStopPropagation: true, isPreventDefault: true};
    Rhui.mobile.swipeLeft(window, function() {
      self.handleKeyDown({ keyCode: 37 });
    }, options);
    Rhui.mobile.swipeUp(window, function() {
      self.handleKeyDown({ keyCode: 38 });
    }, options);
    Rhui.mobile.swipeRight(window, function() {
      self.handleKeyDown({ keyCode: 39 });
    }, options);
    Rhui.mobile.swipeDown(window, function() {
      self.handleKeyDown({ keyCode: 40 });
    }, options);
  },
  beforeDestroy() {
    window.removeEventListener("keydown", this.handleKeyDown.bind(this));
  },
  computed: {
    tiles() {
      return this.board.tiles.filter(tile => tile.value != 0);
    }
  },
  methods: {
    handleKeyDown(event) {
      var self = this;
      if (this.board.hasDone()) {
        return;
      }
      if (event.keyCode >= 37 && event.keyCode <= 40) {
        event.preventDefault && event.preventDefault();
        var direction = event.keyCode - 37;
        this.board.move(direction);
      }

      //press key p
      if (event.keyCode == 80) {
        this.onRestart();
        console.log(this.board.init());
        var myVar = setInterval(myTime, 3000);
        function myTime() {
          if (!self.board.hasDone()) {
            var direction = ~~(Math.random() * 4);
            var result = self.board.step(direction);
            console.log(direction);
            console.log(result);
          } else {
            clearInterval(myVar);
          }
        }
      }
    },
    onRestart() {
      this.board = new Board();
    }
  },
  components: {
    Cell,
    TileView,
    GameEndOverlay
  }
};
</script>
