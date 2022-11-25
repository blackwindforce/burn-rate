<script setup lang="ts">
import { Client } from "boardgame.io/client";
import { onMounted, ref } from "vue";
import { TicTacToe } from "./Game";

const cells = ref<HTMLTableCellElement[]>();
const winner = ref<HTMLParagraphElement>();

const client = Client({ game: TicTacToe });

client.start();

onMounted(() => {
  client.subscribe((state) => {
    if (!state) return;
    console.log(state);

    cells.value?.forEach((cell, index) => {
      const cellValue = state.G.cells[index];
      cell.textContent = cellValue ?? "";
    });

    if (winner.value) {
      if (state.ctx.gameover) {
        winner.value.textContent =
          state.ctx.gameover.winner !== undefined
            ? "Winner: " + state.ctx.gameover.winner
            : "Draw!";
      } else {
        winner.value.textContent = "";
      }
    }
  });
});
</script>

<template>
  <table ref="table">
    <tr v-for="i in 3" :key="i">
      <td
        v-for="j in 3"
        :key="j"
        ref="cells"
        class="cell"
        @click="client.moves.clickCell(3 * (i - 1) + (j - 1))"
      ></td>
    </tr>
  </table>
  <p ref="winner"></p>
</template>

<style scoped>
.cell {
  border: 1px solid #555;
  width: 50px;
  height: 50px;
  line-height: 50px;
  text-align: center;
}
</style>
