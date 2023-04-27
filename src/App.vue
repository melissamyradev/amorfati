<template>
  <p class="title">Amor Fati - Life Quotes</p>
  <div class="quote-container">
    <div v-if="loading" class="loading-spinner"></div>
    <div v-if="!loading">
      <h2 id="author" key="author">{{ author }}</h2>
      <h1 :class="{ animatefadein: !loading }" id="quote" key="quote">"{{ quote }}"</h1>
    </div>
    <img src="./assets/images/pattern-divider-mobile.svg" alt="divider">
    <div @click="rollAndFetch" id="roll">
      <!-- use svg from images folder -->
      <svg>
        <use href="./assets/images/icon-dice.svg#dice" id="icon-dice"/>
      </svg>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  async mounted() {
    await this.fetchQuotes();
    this.roll();
  },
  data() {
    return {
      author: '',
      quote: '',
      currentQuote: {},
      quotesArr: [],
      apiKey: 'vi7KQ3nrzZpgh41kPzrg8A==Ww52qn0TpeEaPVvt',
      loading: true
    }
  },
  methods: {
    async fetchQuotes() {

      const response = await fetch('https://api.api-ninjas.com/v1/quotes?category=life&limit=10',
        //include API key
        { headers: { 'X-Api-Key': this.apiKey } });
      const responseJson = await response.json();

      // filter quotes - only select shorter quotes & don't listen to Trump
      const filteredQuotes = responseJson.filter(quote => quote.quote.length < 200 && quote.author !== 'Donald Trump');

      // store filtered quotes
      filteredQuotes.map(quote => this.quotesArr.push(quote));

    },
    roll() {

      this.loading = true;

      // choose current quote to display;
      this.author = this.quotesArr[0].author;
      this.quote = this.quotesArr[0].quote;
      this.quotesArr.shift();

      // delay display to trigger css animation
      setTimeout(() => {
        this.loading = false;
      }, .1)

    },
    rollAndFetch() {

      this.roll();

      // fetch silently without triggering render
      if (this.quotesArr.length < 5) {
        setTimeout(() => {
          this.fetchQuotes();
        }, 1000);
      }

    }
  }
}
</script>

<style lang="scss">
//fonts
@import url('https://fonts.googleapis.com/css2?family=Manrope:wght@200;300;400;500;600;700;800&display=swap');

//colors
$quote-text-col: #eee;
$accent-col: rgb(82, 255, 168);
$bg-col: rgb(31, 38, 50);
$container-col: rgb(50, 58, 73);

%flex-center-center {
    display: flex;
    justify-content: center;
    align-items: center;
}

%title-text {
    color: $accent-col;
    font-weight: 500;
    text-transform: uppercase;
    letter-spacing: 5px;
}

body {
    background-color: $bg-col;
    font-family: 'Manrope', sans-serif;
    width: 100vw;
    height: 100vh;
    margin: 0;
    flex-direction: column;
    @extend %flex-center-center;
}

h1 {
    font-size: 28px;
    color: $quote-text-col;
}

h2{
    font-size: 13px;    
    margin-bottom: 30px;
    @extend %title-text;
}

img {
    margin-top: 20px;
}

.text {
  display: block;
}

.title {
    font-size: 10px;
    letter-spacing: 5px;
    @extend %title-text;    
    filter: brightness(.5);
    position: fixed;
    bottom: 0;
    left: 0;
    text-align: center;
    width: 100%;
    margin-bottom: 40px;
}

.quote-container {
    background-color: $container-col;
    text-align: center;
    margin-top: 25vh;
    padding:  40px 25px 65px;
    margin: 0 10px 50px;
    border-radius: 25px;
    position: relative;
    box-shadow: 0 0 30px darken($bg-col, 4%);
    @extend %flex-center-center;
    flex-direction: column;
}

.loading-spinner {
    animation: rotate 1s infinite linear;
    width: 30px;
    height: 30px;
    border: 8px solid $accent-col;
    border-bottom: 8px solid $container-col;
    border-radius: 50%;
    margin: 35px;
}

.attribution {
    margin-top: auto;
}

.animatefadein {
    animation: fadein .3s linear forwards;
}

#app {
    @extend %flex-center-center;
    flex-direction: column;
}

#roll {
    border-radius: 50%;
    width: 50px;
    height: 50px;
    padding: 10px;
    background-color: $accent-col;
    margin: 0 auto;
    position: absolute;
    bottom: -32px;
    left: 50%;
    transform: translateX(-50%);
    cursor: pointer;
    @extend %flex-center-center;
}

#roll:hover {
    box-shadow: 0 0 15px  $accent-col;
}

#icon-dice,svg {
    width: 24px;
    height: 24px;
}

@media (min-width: 768px) {
    .quote-container {
        max-width: 500px;
        padding: 40px 35px 65px;
    }
}

@keyframes rotate {
    0% {
        transform: rotate(0deg);
    }

    100% {
        transform: rotate(360deg);
    }
}

@keyframes fadein {
    0% {
        transform: translateY(-10px);
        opacity: 0;
    }

    100% {
        transform: translateY(0);
        opacity: 1;
    }
}
</style>
