<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-title>Comptador App</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content :fullscreen="true">
      <div id="container">
        <ion-button @click="handleButtonClick" :disabled="counterDisabled">{{ funciona ? 'Tap Me' : 'Start Game' }}</ion-button>
        <p>Time: {{ temporitzador }}</p>
        <p>Counter: {{ comptador }}</p>
        <p>Clicks per Second: {{ cps.toFixed(2) }}</p>
      </div>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { ref, onBeforeUnmount } from 'vue';
import { IonButton } from '@ionic/vue';

const INITIAL_TIME = 5;

let comptador = ref(0);
let temporitzador = ref(INITIAL_TIME);
let funciona = ref(false);
let cps = ref(0);

let countdownTimer;
let clickCountTimer;

const inicia = () => {
  countdownTimer = setInterval(() => {
    if (temporitzador.value > 0) {
      temporitzador.value--;
      updatecps();
    } else {
      endGame();
    }
  }, 1000);
};

const handleButtonClick = () => {
  if (!funciona.value) {
    startGame();
  } else {
    incrementar();
  }
};

const startGame = () => {
  inicia();
  init();
  funciona.value = true;
};

const incrementar = () => {
  comptador.value += 1;
};

const endGame = () => {
  clearInterval(countdownTimer);
  clearInterval(clickCountTimer);
  // Show toast message
  const message = `Game Over! Counter: ${comptador.value}`;
  console.log(message);
  reset();
};

const reset = () => {
  comptador.value = 0;
  temporitzador.value = INITIAL_TIME;
  funciona.value = false;
  cps.value = 0;
};

const counterDisabled = ref(false);

const updatecps = () => {
  cps.value = comptador.value / (INITIAL_TIME - temporitzador.value);
};

const init = () => {
  clickCountTimer = setInterval(() => {
    updatecps();
  }, 1000);
};

onBeforeUnmount(() => {
  clearInterval(countdownTimer);
  clearInterval(clickCountTimer);
});
</script>

<style scoped>
#container {
  text-align: center;
  position: absolute;
  left: 0;
  right: 0;
  top: 50%;
  transform: translateY(-50%);
}
</style>
