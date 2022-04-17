<template>
  <button @click="this.visualizeDijkstra()">dijkstra</button>
  <table class="grid">
    <tr v-for="row in this.grid" :key="row.index">
      <th v-for="node in row" :key="node.index">
        <GridNode 
          :col="node.col" 
          :row="node.row" 
          :isStart="node.isStart" 
          :isFinish="node.isFinish" 
          :isWall="node.isWall"
          :isShortestPath="node.isShortestPath"
          :isVisited="node.isVisited"
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
import {dijkstra, getNodesInShortestPathOrder} from '../algorithms/dijkstra.js'

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
      const newGrid = grid.slice();
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
    animateDijkstra(visitedNodesInOrder, nodesInShortestPathOrder) {
      console.log("animateDijkstra");
      for (let i = 0; i <= visitedNodesInOrder.length; i++) {
        if (i === visitedNodesInOrder.length) {
          setTimeout(() => {
            this.animateShortestPath(nodesInShortestPathOrder);
          }, 10 * i);
          return;
        }
        setTimeout(() => {
          const node = visitedNodesInOrder[i];
          this.grid[node.row][node.col].isVisited = true;
        }, 10 * i);
      }
    },
    animateShortestPath(nodesInShortestPathOrder) {
      console.log("animateShortestPath");
      for (let i = 0; i < nodesInShortestPathOrder.length; i++) {
        setTimeout(() => {
          const node = nodesInShortestPathOrder[i];
          this.grid[node.row][node.col].isVisited = false;
          this.grid[node.row][node.col].isShortestPath = true;
        }, 50 * i);
      }
    },
    visualizeDijkstra() {
      const startNode = this.grid[START_NODE_ROW][START_NODE_COL];
      const finishNode = this.grid[FINISH_NODE_ROW][FINISH_NODE_COL];
      const visitedNodesInOrder = dijkstra(this.grid, startNode, finishNode);
      console.log(visitedNodesInOrder);
      const nodesInShortestPathOrder = getNodesInShortestPathOrder(finishNode);
      this.animateDijkstra(visitedNodesInOrder, nodesInShortestPathOrder);
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