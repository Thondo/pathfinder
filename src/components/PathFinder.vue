<template>
  <table class="grid">
    <tr v-for="row in this.grid" :key="row.index">
      <th v-for="node in row" :key="node.index">
        <GridNode 
          :col="node.col" 
          :row="node.row" 
          :isStart="node.isStart" 
          :isFinish="node.isFinish" 
          :isWall="node.isWall"
          @mousedown="this.handleMouseDown(node.row, node.col)"
          @mouseenter="this.handleMouseEnter(node.row, node.col)"
          @mouseup="this.handleMouseUp()"
        />
      </th>
    </tr>
  </table>
</template>

<script>
import GridNode from './node/GridNode.vue'

const START_NODE_ROW = 10;
const START_NODE_COL = 15;
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
      gridHeight: 20,
      gridWidth: 50,
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
    getGridWithWallToggled(grid, row, col) {
      const newGrid = this.grid.slice();
      const node = newGrid[row][col];
      const newNode = {
        ...node,
        isWall: !node.isWall,
      };
      newGrid[row][col] = newNode;
      return newGrid;
    },
    handleMouseDown(row, col) {
      const newGrid = this.getGridWithWallToggled(this.grid, row, col);
      this.grid = newGrid;
      this.mouseIsPressed = !this.mouseIsPressed;
    },
    handleMouseEnter(row, col) {
      if (!this.mouseIsPressed) return;
      const newGrid = this.getGridWithWallToggled(this.grid, row, col);
      this.grid = newGrid;
    },
    handleMouseUp() {
      this.mouseIsPressed = !this.mouseIsPressed;
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