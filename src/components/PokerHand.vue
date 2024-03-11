<template lang="">
  <div class="hand" :class="[isBoard ? 'board' : '']">
    <div
      class="card"
      v-for="card in cards"
      :key="card.valueOf()"
      :class="[card.suit, isHighlight(card) ? 'highlight' : '']"
    >
      {{ card.rank }}
      <span v-if="card.suit === 'S'">&spadesuit;</span>
      <span v-if="card.suit === 'H'">&heartsuit;</span>
      <span v-if="card.suit === 'D'">&#x25C6;</span>
      <span v-if="card.suit === 'C'">&clubs;</span>
    </div>
  </div>
</template>
<script>
export default {
  props: {
    cards: Array,
    best: Array,
    enableHighlight: Boolean,
    isBoard: Boolean,
  },
  methods: {
    isHighlight(card) {
      return (
        this.enableHighlight &&
        this.best &&
        this.best.cards.find(
          (c) => c.value === card.rank && c.suit === card.suit.toLowerCase()
        )
      );
    },
  },
};
</script>
<style>
.hand {
  display: flex;
}
.hand.board .card:nth-of-type(4) {
  margin-left: 15px;
  margin-right: 15px;
}
.card {
  width: 50px;
  height: 60px;
  border-radius: 4px;
  font-size: 20px;
  font-weight: bold;
  margin: 5px;
  padding: 3px;
  color: white;
  box-shadow: 1px 1px 5px black;
  pointer-events: none;
}
.card.S {
  background-color: rgb(55, 55, 55);
}
.card.H {
  background-color: rgb(204, 58, 58);
}
.card.D {
  background-color: rgb(48, 48, 169);
}
.card.C {
  background-color: rgb(35, 137, 35);
}
.card.highlight {
  border: 3px solid yellow;
}
</style>
