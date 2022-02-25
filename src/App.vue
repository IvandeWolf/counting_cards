<template>
  <div class="w-screen h-screen flex flex-col items-center justify-center space-y-4 bg-gray-100 select-none">
    <!-- Restart -->
    <div class="flex space-x-8">
      <div class="flex space-x-2">
        <span>Decks:</span>
        <input type="number" name="numberOfDecks" id="numberOfDecks" v-model="numberOfDecks" min="1" class="w-8" />
      </div>
      <span @click="reset" class="cursor-pointer text-red-700 font-semibold uppercase">Restart</span>
    </div>

    <!-- Card counts -->
    <div class="flex space-x-8 text-lg font-semibold">
      <span>Cards in pile: {{ cardPile.length }}</span>
      <span>Cards discarded: {{ discardPile.length }}</span>
    </div>

    <!-- Playing card -->
    <div @click="drawCard" class="relative h-1/2 aspect-[1/1.5] border flex justify-center items-center rounded-2xl shadow-lg text-4xl bg-white cursor-pointer" :class="colors[card.suit] === 'black' ? 'text-black' : 'text-red-600'">
      <div class="absolute top-2 left-4 flex flex-col space-y-4 items-center justify-center">
        <h2 class="text-6xl font-semibold">{{ card.value }}</h2>
      </div>
      <div class="absolute bottom-2 right-4 flex flex-col space-y-4 items-center justify-center rotate-180">
        <h2 class="text-6xl font-semibold">{{ card.value }}</h2>
      </div>
      <svg v-if="card.suit === 'Spades'" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" aria-hidden="true" role="img" class="h-28 w-28" preserveAspectRatio="xMidYMid meet" viewBox="0 0 16 16"><path fill="currentColor" d="M12.775 5.44C9.751 3.192 8.708 1.393 8.001 0c-.708 1.393-1.75 3.192-4.774 5.44c-5.157 3.833-.303 9.182 3.965 6.238c-.278 1.827-1.227 3.159-2.191 3.733v.59h6v-.59c-.964-.574-1.913-1.906-2.191-3.733c4.268 2.944 9.122-2.405 3.965-6.238z"></path></svg>
      <svg v-else-if="card.suit === 'Clubs'" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" aria-hidden="true" role="img" class="h-28 w-28" preserveAspectRatio="xMidYMid meet" viewBox="0 0 16 16"><path fill="currentColor" d="M12.294 6.137c-.922 0-1.751.384-2.341 1.011c-.25.265-.684.58-1.153.856c.22-.842.917-1.902 1.4-2.367a3.27 3.27 0 0 0 1-2.367C11.2 1.475 9.771.018 8-.001C6.229.018 4.8 1.474 4.8 3.27c0 .932.38 1.771 1 2.367c.484.465 1.18 1.525 1.4 2.367c-.469-.277-.903-.591-1.153-.856a3.197 3.197 0 0 0-2.341-1.011C1.919 6.137.47 7.6.47 9.408s1.448 3.271 3.236 3.271c.923 0 1.751-.396 2.341-1.023c.263-.279.726-.627 1.223-.916c-.047 2.308-1.149 4.003-2.271 4.67V16h6v-.59c-1.122-.668-2.224-2.363-2.271-4.67c.498.289.961.637 1.223.916a3.21 3.21 0 0 0 2.341 1.023c1.787 0 3.236-1.464 3.236-3.271s-1.448-3.271-3.236-3.271z"></path></svg>
      <svg v-else-if="card.suit === 'Hearts'" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" aria-hidden="true" role="img" class="h-28 w-28" preserveAspectRatio="xMidYMid meet" viewBox="0 0 16 16"><path fill="currentColor" d="M11.8 1c-1.682 0-3.129 1.368-3.799 2.797C7.33 2.368 5.883 1 4.201 1a4.202 4.202 0 0 0-4.2 4.2c0 4.716 4.758 5.953 8 10.616c3.065-4.634 8-6.05 8-10.616c0-2.319-1.882-4.2-4.2-4.2z"></path></svg>
      <svg v-else-if="card.suit === 'Diamonds'" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" aria-hidden="true" role="img" class="h-28 w-28" preserveAspectRatio="xMidYMid meet" viewBox="0 0 16 16"><path fill="currentColor" d="M8 0L3 8l5 8l5-8z"></path></svg>
    </div>

    <!-- Count -->
    <div class="group flex space-x-2 text-3xl cursor-pointer font-medium text-gray-700">
      <span v-if="showCount" @click="showCount = false">Count: {{ count }}</span>
      <span v-else @click="showCount = true">Show count</span>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data: () => ({
    cardPile: [],
    discardPile: [],
    card: {
      value: 'A',
      suit: 'Hearts'
    },
    count: 0,
    showCount: false,
    numberOfDecks: 1,
    suits: ['Spades', 'Hearts', 'Diamonds', 'Clubs'],
    colors: { Spades: 'black', Hearts: 'red', Diamonds: 'red', Clubs: 'black' },
    values: ['A', '2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K']
  }),
  mounted () {
    for (let i = 0; i < this.numberOfDecks; i++)
      this.cardPile = this.cardPile.concat(this.createDeck())

    this.shuffle(this.cardPile)

    this.drawCard()
  },
  methods: {
    createDeck () {
      const deck = []
      this.suits.forEach(suit => {
        this.values.forEach(value => {
          deck.push({ value, suit })
        })
      })
      return deck
    },
    drawCard () {
      if (this.cardPile.length === 0) return

      this.card = this.cardPile.pop()
      this.discardPile.push(this.card)

      if (['10', 'J', 'Q', 'K', 'A'].includes(this.card.value))
        this.count++
      if (['2', '3', '4', '5', '6'].includes(this.card.value))
        this.count--
    },
    shuffle (cards) {
      let m = cards.length, t, i

      while (m) {
        i = Math.floor(Math.random() * m--)
        t = cards[m]
        cards[m] = cards[i]
        cards[i] = t
      }

      return cards
    },
    reset () {
      this.cardPile = []
      for (let i = 0; i < this.numberOfDecks; i++)
        this.cardPile = this.cardPile.concat(this.createDeck())
      this.discardPile = []
      this.count = 0
      this.shuffle(this.cardPile)
      this.drawCard()
    }
  },
  watch: {
    numberOfDecks: 'reset'
  }
}
</script>
