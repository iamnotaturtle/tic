<template>
  <div id="app">
    <div class="title">
      Tic Tac Toe
    </div>
    <div class="grid">
      <template v-for="(row, index) in board">
        <div class="item" v-for="(item, key) in row" :key="index + '-' + key"
          @click="select(item, index, key)">
          <transition name="flip" mode="out-in">
            <div v-if="item !== ''" class="content" :class="[getColor(item)]">
              {{ item }}
            </div>
          </transition>
        </div>
      </template>
    </div>
    <div v-if="result" class="msg">
      {{ msg }}
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';

@Component
export default class App extends Vue {
  result: boolean = false;

  grid: any[] = [
    ['', '', ''],
    ['', '', ''],
    ['', '', ''],
  ];

  currentType: string = 'X';

  getColor = (item: string): string => (item === 'X' ? 'green' : 'red')

  get board(): any {
    return this.grid;
  }

  get msg(): string {
    return `Congrats! ${this.currentType} won!`;
  }

  select(item: string, row: number, column: number) {
    if (item || this.result) {
      return;
    }

    const currentRow = this.grid[row].slice(0);
    currentRow[column] = this.currentType;
    this.$set(this.grid, row, currentRow);
    this.result = this.didWin(row, column);

    if (!this.result) {
      this.currentType = this.currentType === 'X' ? 'O' : 'X';
    }
  }

  didWin(row: number, column: number): boolean {
    const type = this.grid[row][column];

    if (!type) {
      return false;
    }

    const rowResult = this.grid[row].every((value: string) => value === type);
    const columnResult = this.grid.every((gridRow: string[]) => gridRow[column] === type);
    const leftDiagonal = this.grid.every((gridRow: string[], index) => gridRow[index] === type);
    const rightDiagonal = this.grid.every(
      (gridRow: string[], index) => gridRow[gridRow.length - 1 - index] === type,
    );
    return rowResult || columnResult || leftDiagonal || rightDiagonal;
  }
}
</script>


<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

.grid {
  display: grid;
  justify-content: center;
  grid-template-columns: 50px 50px 50px;
  grid-gap: 5px;
}

.item {
  width: 50px;
  height: 50px;
  display: flex;
  align-items: center;
  justify-content: center;
  user-select: none;
  background: white;
  border: 1px solid darkgrey
}

.flip-enter-active {
  transition: all .2s cubic-bezier(0,.75,.25,1); //ease-in-quad
}

.flip-leave-active {
  transition: all .25s cubic-bezier(0,.75,.25,1); //ease-out-quad
}

.flip-enter, .flip-leave-to {
  transform: scaleY(0) translateZ(0);
  opacity: 0;
}

.content {
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  background: lightgreen;
}

.green {
  background: lightgreen;
}

.red {
  background: lightsalmon;
}
</style>
