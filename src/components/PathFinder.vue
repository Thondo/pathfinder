<template>
  <div class="grid">
    <div v-for="row in this.grid" :key="row.index">
      <div v-for="node in row" :key="node.index">
        <GridNode 
          :col="node.col" 
          :row="node.row" 
          :isStart="node.isStart" 
          :isFinish="node.isFinish" 
          :isWall="node.isWall"
        />
      </div>
    </div>
  </div>
</template>

<script>
import GridNode from './node/GridNode.vue'

const START_NODE_ROW = 0;
const START_NODE_COL = 0;
const FINISH_NODE_ROW = 10;
const FINISH_NODE_COL = 35;

export default {
  name: 'PathFinder',
  components: {
    GridNode,
  },
  data() {
    return {
      grid: [],
      gridHeight: 40,
      gridWidth: 40,
      mouseIsPressed: false,
    }
  },
  methods: {
    getInitialGrid() {
      const grid = [];
      for (let row = 0; row < this.gridHeight; row++) {
        const currentRow = [];
        for (let col = 0; col < this.gridWidth; col++) {
          currentRow.push(this.createNode(col, row));
        }
        grid.push(currentRow);
      }
      return grid;
    },
    createNode(col, row) {
      return {
        col,
        row,
        isStart: row === START_NODE_ROW && col === START_NODE_COL,
        isFinish: row === FINISH_NODE_ROW && col === FINISH_NODE_COL,
        distance: Infinity,
        isVisited: false,
        isWall: false,
        previousNode: null,
      };
    },

  },
  mounted() {
    this.grid = this.getInitialGrid();
  }
}
</script>

<style scoped>
  .grid {
    margin: 100px 0 0;
  }
</style>