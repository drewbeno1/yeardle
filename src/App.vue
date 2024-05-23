<script setup>
import SimpleKeyboard from "./components/SimpleKeyboard.vue";
import WordRow from "./components/WordRow.vue";
import { reactive, ref, onMounted, computed } from "vue";
import Header from "./components/Header.vue";
import WinPage from "./components/WinPage.vue";
import LosePage from "./components/LosePage.vue";

import images from './assets/images.js'
const imageArray = images.images

const state = reactive({
  solution: "books",
  darkmode: false,
  guesses: ["", "", "", "", ""],
  currentGuessIndex: 0,
  guessedNumber: {
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

const wonGame = ref(false);
const lostGame = ref(false);

const handleInput = (key) => {
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
    // Check if the game is won
    if (currentGuess === state.solution) {
      wonGame.value = true;
    }
    // Check if the game is lost
    if (!wonGame.value && state.currentGuessIndex >= 5) {
      lostGame.value = true;
    }
    //loop over every number in guess
    for (var i = 0; i < currentGuess.length; i++) {
      let c = currentGuess.charAt(i)
      //check if current number matches solution
      if (c == state.solution.charAt(i)) {
        state.guessedNumber.found.push(c);
      //if not negative 1, number is somewhere in solution
      } else if (state.solution.indexOf(c) != -1) {
        state.guessedNumber.hint.push(c);
      } else {
        state.guessedNumber.miss.push(c);
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

const shareScore = () => {
  const emojiMap = {
    'green': '✅',
    'yellow': '🟡',
    'gray': '⬛',
  };

  const score = state.guesses.map(guess => {
    let s = state.solution;
    let v = guess;

    let temp = ["gray", "gray", "gray", "gray"];
    let letterPool = [];
    for (let i = 0; i < 4; i++) {
      if (s.charAt(i) == v.charAt(i)) {
        temp[i] = "green";
      } else {
        letterPool.push(s.charAt(i));
      }
    }

    // If all elements are green, return the mapped string immediately
    if (temp.every(color => color === "green")) {
      return temp.map(color => emojiMap[color]).join('');
    }

    for (let i = 0; i < 4; i++) {
      if (temp[i] == "gray") {
        if (letterPool.indexOf(v.charAt(i)) != -1) {
          letterPool.splice(letterPool.indexOf(v.charAt(i)), 1)
          temp[i] = "yellow";
        }
      }
    }
    return temp.map(color => emojiMap[color]).join('');
  }).join('\n');

  let lostMessage = '';
  if (state.lostGame) {
    lostMessage = 'Captain Butthole! 🟥';
  }
  const shareData = {
    title: 'Play Yeardle!',
    text: `${score}\n${lostMessage}`,
    url: 'https://yeardle.io',
  }

  try {
    navigator.share(shareData)
      .then(() => console.log('Successful share'))
      .catch((error) => console.log('Error sharing', error));
  } catch(err) {
    console.error('Could not share score: ', err);
  }
}

function resetGame() {
  document.location.reload(true)
}
</script>

<template>
<div :class="{ darkbg: state.darkmode }" class="flex flex-col justify-center">
  <div class="flex flex-row px-16 items-center justify-between">
    <Header />
  </div>
  <div class="flex flex-col max-w-md mx-auto justify-evenly">
    <img class="max-w-[80vw] mb-4" :src="state.currentImage" alt="Sorry, I forgot to pick the images for this week!">
    <div class="overflow-auto mb-4">
      <word-row
        v-for="(guess, i) in state.guesses"
        :key="i"
        :value="guess"
        :solution="state.solution"
        :submitted="i < state.currentGuessIndex"
        :darkmode=state.darkmode
      />
    </div>
    <WinPage v-if="wonGame" :shareScore="shareScore" />
    <LosePage v-if="lostGame" :shareScore="shareScore" />
    <div>
    <!-- listen for event -->
    <simple-keyboard
      @onKeyPress="handleInput"
      :guessedNumber="state.guessedNumber"
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
