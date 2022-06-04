<template>
  <div class="container">
    <h1>War Card Game!</h1>
    <div v-if="!finJgo" id="game">
      <div class="cartas" id="scoreboard">
        <div class="score">
          <h2>Player One: {{ ptsJug1 }} | Player Two: {{ ptsJug2 }}</h2>
        </div>

        <button @click="callApi()">Draw Cards</button>
      </div>


      <div class="jugadores">
        <div class="uno" id="playerOne">
          <h2 class="hdos">Player One</h2>
          <div class="card"><img :src="carta1?.images?.png" /></div>
        </div>
        <div class="uno" id="playerTwo">
          <h2 class="hdos">Player Two</h2>
          <div class="card"><img :src="carta2?.images?.png" /></div>
        </div>
      </div>

    </div>
    <div v-if="finJgo">
      <button @click="obtengoMazo()">Play Game</button>
    </div>
  </div>

</template>

<script>
import { ref } from "vue";
import axios from "axios";
function translateCards(value) {
  switch (value) {
    case "JACK":
      value = "11";
      break;

    case "QUEEN":
      value = "12";
      break;

    case "KING":
      value = "13";
      break;

    case "ACE":
      value = "14";
      break;
    default:
      break;
  }
  return value;
}
export default {
  name: "App",
  setup() {

    let finJgo = ref(true),
      carta1 = ref({}),
      carta2 = ref({}),
      mazoId = ref(null),
      ptsJug1 = ref(0),
      ptsJug2 = ref(0);

    async function obtengoMazo() {
      finJgo.value = false;
      if (mazoId.value === null) {
        //create a new deck
        const { data } = await axios.get(
          "https://deckofcardsapi.com/api/deck/new/shuffle/?deck_count=1"
        )
          .catch(error => {
            console.log('Error: ', error)
          })
          ;
        mazoId.value = data.deck_id;
      }
    }

    async function callApi() {
      const { data } = await axios.get(
        "https://deckofcardsapi.com/api/deck/" + mazoId.value + "/draw/?count=2"
      )
        .catch(error => {
          console.log('Error: ', error)
        });
      console.log(data);
      const remaining = data.remaining;
      const { cards } = data;
      carta1.value = cards[0];
      carta2.value = cards[1];
      const valueOne = parseInt(translateCards(carta1.value.value));
      const valueTwo = parseInt(translateCards(carta2.value.value));
      if (valueOne > valueTwo) ptsJug1.value += 1;
      if (valueOne < valueTwo) ptsJug2.value += 1;

      if (remaining === 0) {
        finJgo.value = true;
        if (ptsJug1.value > ptsJug2.value) {
          alert("Player One Wins!");
        } else if (ptsJug1.value < ptsJug2.value) {
          alert("Player Two Wins!");
        }
        location.reload()
      }
    }

    return {
      callApi,
      carta1,
      carta2,
      ptsJug1,
      ptsJug2,
      finJgo,
      obtengoMazo,
    };
  },
};
</script>

<style>
body {
  background-image: url("https://images.unsplash.com/photo-1506362802973-bd1717de901c?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=837&q=80");
  background-size: cover;
  background-repeat: no-repeat;
  margin: 0;
  padding: 0;
}

h1 {
  font-size: 50px;
  color: #b8b8d1;
}

.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100vh;
  transform: translateY(-5%);
  width: 100vw;
}

h2 {
  font-size: 25px;
  transform: translateY(-20%);
  color: #b8b8d1;
}

.jugadores {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: center;
  gap: 50px;
}

.cartas {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  transform: translateY(-10%);
}

.uno {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

button {
  font-size: 20px;
  padding: 10px;
  border-radius: 5px;
  background-color: #b8b8d1;
  border: 1px solid #ccc;
  cursor: pointer;
}

@media only screen and (max-width: 576px) {

  .container {
    transform: scale(0.7);
  }

  h1 {
    font-size: 40px;
    margin-top: -10vh;
  }

  h2 {
    font-size: 20px;
    transform: translateY(-20%);
  }

  .hdos {
    font-size: 30px;
  }

  .jugadores {
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: center;
    gap: 30px;
    transform: scale(0.6);
    margin-top: -20px;
  }

  .cartas {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    transform: translateY(-10%);
  }

  .uno {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
  }

  button {
    font-size: 25px;
  }
}
</style>
