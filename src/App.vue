<script>
import { ref } from "vue"

export default {
  name: "App",
  setup() {
    const minesLaid = ref(false)
    const minesCount = ref(40)
    const minesIndex = ref([])
    const gameOver = ref(false)

    const board = ref([
      "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "",
      "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "",
      "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "",
      "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "",
      "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "",
      "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "",
      "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "",
      "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "",
      "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "",
      "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "",
      "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "",
      "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "",
      "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "",
      "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "",
      "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "",
      "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", ""
    ])

    const layMines = (cellIndex) => {
      for (let i = 0; i < minesCount.value; i++) {
        let mineIndex = Math.round(Math.random() * 255)
        while (mineIndex === cellIndex || minesIndex.value.indexOf(mineIndex) !== -1) {
          mineIndex = Math.round(Math.random() * 255)
        }
        minesIndex.value.push(mineIndex)
      }
      minesLaid.value = true
    }

    const computeIndexList = (cellIndex) => {
      let indexList = [cellIndex - 16, cellIndex + 16]
      if ((cellIndex + 1) % 16 !== 0) indexList = [...indexList, cellIndex + 1, cellIndex + 17, cellIndex - 15]
      if ((cellIndex + 1) % 16 !== 1) indexList = [...indexList, cellIndex - 1, cellIndex - 17, cellIndex + 15]
      return indexList.filter(index => index >= 0 && index <= 255)
    }

    const onClick = (cellIndex) => {
      if (gameOver.value) return
      if (!minesLaid.value) layMines(cellIndex)
      if (board.value[cellIndex] !== "" && minesIndex.value.indexOf(cellIndex) === -1) return
      if (minesIndex.value.indexOf(cellIndex) !== -1) {
        gameOver.value = true
        return
      }
      
      const indexList = computeIndexList(cellIndex)

      const mineCount = indexList.reduce((acc, index) => {
        return minesIndex.value.indexOf(index) !== -1 ? acc + 1 : acc
      }, 0)

      board.value[cellIndex] = mineCount
      
      if (mineCount === 0) indexList.forEach(index => onClick(index))
    }

    const rightClick = (cellIndex) => {
      if (gameOver.value) return
      if (board.value[cellIndex] !== "" && board.value[cellIndex] !== "🔶") return
      if (board.value[cellIndex] === "🔶")
        board.value[cellIndex] = ""
      else
        board.value[cellIndex] = "🔶"
    }

    const COLORS = [
      "text-gray-500",
      "text-blue-500",
      "text-green-500",
      "text-red-500",
      "text-blue-800",
      "text-red-800"
    ]

    return { board, minesIndex, onClick, rightClick, gameOver, COLORS }
  }
}
</script>

<template>
  <h2 class="text-3xl">💥 Minesweeper 💥</h2>
  <div class="bg-white text-gray-600 rounded-lg p-10 mt-10">
    <div class="flex flex-wrap w-[514px] border border-gray-300">
      <div
        class="w-8 h-8 flex items-center justify-center border font-semibold transition hover:bg-gray-50 border-gray-300 cursor-pointer"
        :class="COLORS[Number(cell)] ?? ''"
        v-for="(cell, cellIndex) in board"
        @click="() => onClick(cellIndex)"
        @contextmenu.prevent="() => rightClick(cellIndex)"
      >
        {{ gameOver && minesIndex.indexOf(cellIndex) !== -1 ? "◼️" : cell }}
      </div>
    </div>
  </div>
  <div class="absolute bottom-0 left-0 w-full flex justify-center py-4 text-gray-500 text-sm">
    created by
    <a href="https://twitter.com/muitogoxtosa" target="blank" class="mx-1 text-blue-500">@muitogoxtosa</a>
    with ❤️
  </div>
</template>