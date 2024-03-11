<template>
  <button @click="newHand">New hand</button>
  <div>
    <h3>Board</h3>
    <PokerHand :cards="board" isBoard />
  </div>

  <div
    v-for="(player, index) in players"
    :key="index"
    class="player"
    @click="player.showResult = !player.showResult"
  >
    <h3>
      Player {{ index + 1
      }}<span v-if="player.showResult">: {{ player.best.descr }}</span>
    </h3>
    <PokerHand
      :cards="player.hand"
      :best="player.best"
      :enableHighlight="player.showResult"
    />
  </div>
  <hr />
  <div>
    <label>
      Players:<select v-model="playerCount">
        <option v-for="i in 5" :key="i">{{ i }}</option>
      </select>
    </label>
  </div>
  <div>
    <label for="showResults">
      <input type="checkbox" id="showResults" v-model="showResults" />
      Show results automatically
    </label>
  </div>
  <p>
    Created by <a href="https://vbeaulieu.com">vbeaulieu</a>, using npm packages
    <a href="https://www.npmjs.com/package/pokersolver">pokersolver</a> and
    <a href="https://www.npmjs.com/package/deckofcards">deckofcards</a>.
  </p>
</template>

<script>
import { Deck } from "deckofcards";
import { Hand } from "pokersolver";
import PokerHand from "@/components/PokerHand";
export default {
  name: "App",
  components: { PokerHand },
  data() {
    return {
      players: [],
      board: [],
      deck: null,
      playerCount: 2,
      showResults: false,
    };
  },
  methods: {
    newHand() {
      this.players = [];
      this.board = [];
      this.deck = new Deck();

      for (let b = 0; b < 5; b++) {
        this.board.push(this.deck.draw());
      }

      for (let i = 0; i < this.playerCount; i++) {
        this.players.push({
          hand: [],
          best: null,
          showResult: this.showResults,
        });
        for (let c = 0; c < 4; c++) {
          this.players[i].hand.push(this.deck.draw());
        }
        this.players[i].best = this.bestPloHand(
          this.players[i].hand,
          this.board
        );
      }
    },
    get2Cards(hand) {
      let possible = [];
      for (let i = 0; i < hand.length - 1; i++) {
        for (let j = i + 1; j < hand.length; j++) {
          possible.push([hand[i].valueOf(), hand[j].valueOf()]);
        }
      }
      return possible;
    },
    get3Cards(hand) {
      let possible = [];
      for (let i = 0; i < hand.length - 2; i++) {
        for (let j = i + 1; j < hand.length - 1; j++) {
          for (let k = j + 1; k < hand.length; k++) {
            possible.push([
              hand[i].valueOf(),
              hand[j].valueOf(),
              hand[k].valueOf(),
            ]);
          }
        }
      }
      return possible;
    },
    bestPloHand(hand, board) {
      let possibleHands = [];
      const twoCardsHand = this.get2Cards(hand);
      const threeCardBoard = this.get3Cards(board);
      for (let i = 0; i < twoCardsHand.length; i++) {
        for (let j = 0; j < threeCardBoard.length; j++) {
          possibleHands.push(
            Hand.solve(twoCardsHand[i].concat(threeCardBoard[j]))
          );
        }
      }
      const best = Hand.winners(possibleHands)[0];
      return best;
    },
  },
  created() {
    this.newHand();
  },
};
</script>

<style>
* {
  box-sizing: border-box;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}
.player {
  margin: 10px 0;
}
button {
  padding: 1em 3em;
  background-color: rgb(124, 100, 233);
  box-shadow: 1px 1px 5px black;
  color: white;
  border-radius: 5px;
  font-weight: bold;
  border: none;
  cursor: pointer;
}
select {
  padding: 0.5em 2em;
  border-radius: 5px;
  margin-left: 10px;
}
</style>
