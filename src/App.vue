<script setup>
import SimpleKeyboard from "./components/SimpleKeyboard.vue";
import WordRow from "./components/WordRow.vue";
import { reactive, ref, onMounted, computed } from "vue";
import Header from "./components/Header.vue";

import images from './assets/images.js'
const imageArray = images.images

const state = reactive({
  solution: "books",
  darkmode: false,
  guesses: ["", "", "", "", ""],
  currentGuessIndex: 0,
  guessedLetters: {
    //black
    miss: [],
    //green
    found: [],
    //yellow
    hint: [],
  },
  currentImage: "",
})

const getWord = () => {
  const today = new Date().toLocaleString('default', { weekday: 'long' });

  let image = imageArray.find(image => image.day === today);
  if (image) {
    state.solution = image.year;
    state.currentImage = image.src;
  }
}
getWord()

const wonGame = computed(
  () =>
  //check if last guess is equal to solution
    state.guesses[state.currentGuessIndex - 1] === state.solution
)

const lostGame = computed(() => !wonGame.value
  && state.currentGuessIndex >= 6
)

const handleInput = (key) => {
  console.log(key)
  //first check if current guess index is >= 6 -- no typing if out of guesses
  //disable input if we've won
  if (state.currentGuessIndex >= 5 || wonGame.value) {
    return
  }
  const currentGuess = state.guesses[state.currentGuessIndex]

  if (key == "{enter}") {
    //send guess, check if 4 numbers entered
    if (currentGuess.length == 4) {
      state.currentGuessIndex++;
      //loop over every number in guess
      for (var i = 0; i < currentGuess.length; i++) {
        let c = currentGuess.charAt(i)
        //check if current number matches solution
        if (c == state.solution.charAt(i)) {
          state.guessedNumbers.found.push(c);
        //if not negative 1, number is somewhere in solution
        } else if (state.solution.indexOf(c) != -1) {
          state.guessedNumbers.hint.push(c);
        } else {
          state.guessedNumbers.miss.push(c);
        }
      }
    }
  } else if (key == "{bksp}") {
    //remove last number
    //set state to current index & chop off last char
    state.guesses[state.currentGuessIndex] =
      currentGuess.slice(0, -1)
  } else if (currentGuess.length < 4) {
    //add number if it's a number
    if (!isNaN(key)) {
      state.guesses[state.currentGuessIndex] += key;
    }
  }
}

onMounted(() => {
  window.addEventListener("keyup", (e) => {
    e.preventDefault()
    //get key
    let key =
      //in order to pass the same enter & bckspc values, create ternary operator
      //make lowerspace to match virtual keyboard
      e.key == "Enter"
        ? "{enter}"
        : e.key == "Backspace"
        ? "{bksp}"
        : e.key
        // : String.fromCharCode(e.key).toLowerCase();
    handleInput(key);
  });
})


function resetGame() {
  document.location.reload(true)
}

</script>

<template>

<div :class="{ darkbg: state.darkmode }">
  <div class="flex flex-row px-16 h-20 items-center justify-between">
    <Header />
  </div>
  <div class="flex flex-col h-[calc(100vh_-_7rem)] max-w-md mx-auto justify-evenly">
    <img class="max-w-[80vw] ml-11 md:ml-0 mb-4" :src="state.currentImage" alt="Sorry, I forgot to pick the images for this week!">
    <div class="max-h-30 overflow-auto mb-4">
      <word-row
        v-for="(guess, i) in state.guesses"
        :key="i"
        :value="guess"
        :solution="state.solution"
        :submitted="i < state.currentGuessIndex"
        :darkmode=state.darkmode
      />
    </div>
    <p v-if="wonGame" :class="state.darkmode ? 'darktxt' : 'lighttxt'">
      WOW! You solved it! 😸
      <br>
      <br>
      <button @click="resetGame" class="text-green-600   bg-transparent border border-solid border-green-600 hover:bg-green-700 hover:text-white active:bg-green-700 font-bold uppercase text-sm px-6 py-3 rounded outline-none focus:outline-none mr-1 mb-1 ease-linear transition-all duration-150">
        Play again!
      </button>
    </p>
    <p v-else-if="lostGame" :class="state.darkmode ? 'darktxt' : 'lighttxt'">
      Out of tries! 😿 <b>Solution: {{ state.solution }}</b>
      <br>
      <br>
      <button @click="resetGame" class="text-green-600   bg-transparent border border-solid border-green-600 hover:bg-green-700 hover:text-white active:bg-green-700 font-bold uppercase text-sm px-6 py-3 rounded outline-none focus:outline-none mr-1 mb-1 ease-linear transition-all duration-150">
        Share with Friends
      </button>
    </p>
    <div>
    <!-- listen for event -->
    <simple-keyboard
      @onKeyPress="handleInput"
      :guessedLetters="state.guessedLetters"
    />
    </div>
  </div>
</div>

</template>

<style lang="postcss">
div.darkbg {
  @apply bg-gradient-to-tl from-gray-800 via-gray-900 to-black;
}

p.darktxt {
  @apply text-center;
  @apply text-slate-100;
}

p.lighttxt {
  @apply text-black;
  @apply text-center;
}
</style>
