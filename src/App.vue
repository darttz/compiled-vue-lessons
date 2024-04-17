<script setup>
import { ref, computed } from 'vue'
const goatCount = ref(5)
const requiredFood = computed(() => {
  return goatCount.value * 3
})
const multiplyGoatsByFive = () => {
  goatCount.value *= 5
}
const remainingCardCount = ref(0)
const lastApiResponse = ref()
const currentDeckId = ref()
const lastCard = ref()
const currentHand = ref([])
const createNewDeck = () => {
  lastApiResponse.value = 'We tried to create a deck!'
  fetch('https://www.deckofcardsapi.com/api/deck/new/shuffle/?deck_count=1')
    .then((response) => {
      return response.json()
    })
    .then((data) => {
      lastApiResponse.value = data
      currentDeckId.value = data.deck_id
      lastCard.value = undefined
      remainingCardCount.value = data.remaining
      currentHand.value = []
    })
}
const shuffleDeck = () => {
  lastApiResponse.value = 'We tried to shuffle a deck!'
  const deckId = currentDeckId.value || 'new'
  fetch(`https://www.deckofcardsapi.com/api/deck/${deckId}/shuffle/?deck_count=1`)
    .then((response) => {
      return response.json()
    })
    .then((data) => {
      lastApiResponse.value = data
      currentDeckId.value = data.deck_id
      lastCard.value = undefined
      remainingCardCount.value = data.remaining
      currentHand.value = []
    })
}
const drawCard = () => {
  lastApiResponse.value = 'We tried to draw a card!'
  const deckId = currentDeckId.value || 'new'
  fetch(`https://www.deckofcardsapi.com/api/deck/${deckId}/draw/?count=1`)
    .then((response) => {
      return response.json()
    })
    .then((data) => {
      lastApiResponse.value = data
      currentDeckId.value = data.deck_id
      lastCard.value = data.cards[0]
      remainingCardCount.value = data.remaining
      currentHand.value.push(lastCard.value)
    })
}
</script>

<template>
  <div class="pokerTable">
    <header>
      <img alt="Vue logo" class="logo" src="./assets/logo.svg" width="125" height="125" />
      <h1>text here</h1>
    </header>

    <main>
      <p>We got some main content fam.</p>
      <p>We have {{ goatCount }} goats!</p>
      <p>We need to have {{ requiredFood }} tin cans to feed them!</p>
      <div>
        <button @click="goatCount += 1">Add 1 Goat</button>
        <button @click="goatCount += 4">Add 4 Goats</button>
        <button @click="multiplyGoatsByFive">Multiply Goats by 5</button>
      </div>
      <div>
        <h2>Playin some cards</h2>
        <div>
          <button @click="createNewDeck">Create New Deck</button>
          <button @click="shuffleDeck">Shuffle Deck</button>
          <button @click="drawCard" :disabled="currentHand.length >= 5">Draw a Card</button>
        </div>
        <h3>Current Deck ID: {{ currentDeckId }}</h3>
        <h3>Remaining Cards: {{ remainingCardCount }}</h3>

        <div>
          <h4>Last API response:</h4>
          <pre>{{ lastApiResponse }}</pre>
          <h4>Last Card:</h4>
          <pre>{{ lastCard }}</pre>
        </div>
      </div>
      <div v-if="lastCard">
        <img
          :src="lastCard.image"
          :title="lastCard.value + ' of ' + lastCard.suit"
          :alt="lastCard.value + ' of ' + lastCard.suit"
        />
      </div>
      <div>
        <img
          v-for="(card, index) in currentHand"
          :key="index"
          :src="card.image"
          :title="card.value + ' of ' + card.suit"
          :alt="card.value + ' of ' + card.suit"
          :width="50 + index * 5"
        />
      </div>
    </main>
  </div>
</template>

<style scoped>
header {
  line-height: 1.5;
}
header h1 {
  color: red;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}
.pokerTable {
  /* background: url(./assets/pokertable.jpg); */
  background-color: green;
  padding: 20px;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
