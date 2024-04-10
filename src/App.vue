<script setup>
import TheCard from './components/TheCard.vue'
import { ref, computed } from 'vue'


const GRID = {
  row: 10,
  col: 10
}
const initGrid = (row, col) => {
  const ret = Array.from({ length: row * col }, (_, i) => {
    const x = i % col
    const y = Math.floor(i / col)
    return {
      id: i + 1,
      x: x * 100,
      y: y * 100
    }
  })
  return ret;
}
const grid = ref(initGrid(GRID.row, GRID.col))
const isPress = ref(false)

const pointerdown = (e) => {
  isPress.value = true
  const { clientX, clientY } = e
  last.x = clientX
  last.y = clientY
}
const delta = {
  x: 0,
  y: 0
}
const last = {
  x: 0,
  y: 0
}

const position = ref({ x: 0, y: 0 })
const pointermove = (e) => {
  if (!isPress.value) return
  const { clientX, clientY } = e
  delta.x = clientX - last.x
  delta.y = clientY - last.y
  
  position.value.x += delta.x * 10
  position.value.y += delta.y * 10
  last.x = clientX
  last.y = clientY
  
}

const pointerup = (e) => {
  isPress.value = false
}

const getCardPositionFromBasePos = (position) => {
  const range = {
    startX: -Math.floor(position.x / 100) - 1,
    startY: -Math.floor(position.y / 100) - 1,
    endX: -Math.floor(position.x / 100) - 1 + 3,
    endY: -Math.floor(position.y / 100) - 1 + 3
  }
  // create a 4x4 grid from range
  let array = [];
  const { startX, startY, endX, endY } = range;
  for (let y = startY; y <= endY; y++) {
    let row = [];
    for (let x = startX; x <= endX; x++) {
      // 可以在這裡自定義每個元素的值，下面的例子是使用一個包含行和列索引的對象
      row.push([x, y]);
    }
    array.push(row);
  }
  return array.flat();
  
}

const currentWindow = computed(() => {
  const basePos = {
    x: -Math.floor(position.value.x / 100) - 1,
    y: -Math.floor(position.value.y / 100) - 1
  }
  // from 10 x 10 grid

  // x = 0, y = 0
  // [0, 1, 2]
  // [10, 11, 12]
  // [20, 21, 22]

  // x = 1, y = 0
  // [1, 2, 3]
  // [11, 12, 13]
  // [21, 22, 23]

  // x = -1, y = 0
  // [10, 0, 1]
  // [20, 11, 12]
  // [30, 21, 22]


  const newPos = getCardPositionFromBasePos(position.value)
  return generateGrid(basePos, GRID.row)
    .flat()
    .map((id, idx) => {
      grid.value[id].x = newPos[idx][0] * 100
      grid.value[id].y = newPos[idx][1] * 100
      return grid.value[id]
    });
})

const windowLength = 4
function generateGrid(basePos, gridWidth = 10) {
  return Array.from({ length: windowLength }, (_, y) => {
    return Array.from({ length: windowLength }, (_, x) => {
      const currentX = (((basePos.x + x) % gridWidth) + gridWidth) % gridWidth;
      const currentY = (((basePos.y + y) % gridWidth) + gridWidth) % gridWidth;
      return currentX + currentY * gridWidth;
    });
  });
}

</script>

<template>
  
  <div>{{ currentWindow.map(c => c.id) }}</div>
  <div class="wrapper"
    @pointerdown="pointerdown"
    @pointermove="pointermove"
    @pointerup="pointerup"
  >
    <div
      class="window"
      :style="{ transform: `translate(${position.x}px, ${position.y}px)` }"

    >
      <TheCard
        v-for="card in currentWindow"
        :id="card.id"
        :key="card.id"
        :style="{ left: `${card.x}px`, top: `${card.y}px` }"
      />
    </div>
  </div>
</template>

<style scoped>
.wrapper {
  position: relative;
  width: 300px;
  overflow: hidden;
  height: 300px;
  border: 1px solid #000;
  
}

.window {
}
</style>
