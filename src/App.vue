<script setup>
import {ref, computed, onMounted} from 'vue'

const wins = ref(0)
const losses = ref(0)
const draws = ref(0)

const choice = ref(null)
const computerChoice = ref(null)
const verdict = ref(null)

const outcomes = {
  pedra:{
    pedra:'draw',
    papel: 'loss',
    tesoura: 'wins',
  },

  papel:{
    pedra: 'win',
    papel: 'draw',
    tesoura: 'loss'
  },

  tesoura:{
    pedra: 'loss',
    papel: 'win',
    tesoura: 'draw'
  }
}

const play = c => {
  choice.value = c

  const choices = ['pedra', 'papel', 'tesoura']
  const random = Math.floor(Math.random() * choices.length)
  computerChoice.value = choices[random]

  const outcome = outcomes[c][computerChoice.value]

  if (outcome === 'win') {
		wins.value++
		verdict.value = 'Você venceu!'
	} else if (outcome === 'loss') {
		losses.value++
		verdict.value = 'Você perdeu!'
	} else {
		draws.value++
		verdict.value = 'Empate!'
	}
	SaveGame()
}

const winPercentage = computed(() => {
	const total = wins.value + draws.value + losses.value
	return total ? (wins.value / total) * 100 : 0
})

const SaveGame = () => {
	localStorage.setItem('wins', wins.value)
	localStorage.setItem('draws', draws.value)
	localStorage.setItem('losses', losses.value)
}

const LoadGame = () => {
	wins.value = parseInt(localStorage.getItem('wins')) || 0
	draws.value = parseInt(localStorage.getItem('draws')) || 0
	losses.value = parseInt(localStorage.getItem('losses')) || 0
}

const ResetRound = () => {
	choice.value = null
	computerChoice.value = null
	verdict.value = null
}

onMounted(() => {
	LoadGame()
	window.addEventListener('keypress', (e) => {
		if (e.key === 'r') {
			ResetRound()
		}
	})
})

</script>

<template>
  <div class="bg-gray-700 text-white text-center min-h-screen flex flex-col">
		<header class="container mx-auto p-6">
			<h1 class="text-4xl font-bold">Pedra, papel, tesoura!</h1>
		</header>

		<main class="container mx-auto p-6 flex-1">
			<div v-if="choice === null" class="flex items-center justify-center -mx-6">

				<button @click="play('pedra')"
					class="bg-white rounded-lg shadow-lg w-64 p-6 mx-2 transition-colors duration-300 hover:bg-pink-500">
					<img src="./assets/RockIcon.svg" alt="Rock" class="w-full" />
				</button>

				<button @click="play('papel')"
					class="bg-white rounded-lg shadow-lg w-64 p-6 mx-2 transition-colors duration-300 hover:bg-green-500">
					<img src="./assets/PaperIcon.svg" alt="Paper" class="w-full" />
				</button>

				<button @click="play('tesoura')"
					class="bg-white rounded-lg shadow-lg w-64 p-6 mx-2 transition-colors duration-300 hover:bg-yellow-500">
					<img src="./assets/ScissorsIcon.svg" alt="Scissors" class="w-full" />
				</button>

			</div>

			<div v-else>
				<div class="text-3xl mb-4">
					Você escolheu <span class="text-pink-500">{{ choice }}</span>
				</div>
				<div class="text-3xl mb-4">
					O computador escolheu <span class="text-green-500">{{ computerChoice }}</span>
				</div>
				<div class="text-6xl mb-12">
					{{ verdict }}
				</div>

				<button @click="ResetRound" class="bg-pink-500 text-lg py-2 px-4">Jogue novamente</button>
			</div>

			<div class="mt-12 text-3xl mb-4">{{ wins }} : {{ draws }} : {{ losses }}</div>

			<div class="text-lg">Taxa de vitória: {{ Math.round(winPercentage) }}%</div>
		</main>

		<footer class="container mx-auto p-6">
			<button><a href="https://github.com/jxhnlcs/RockPaperScissors" class="box">Click para ver o repositório!</a></button>
		</footer>
	</div>
</template>