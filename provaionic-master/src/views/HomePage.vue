<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-title>Comptador App</ion-title>
        <ion-buttons slot="primary">
          <ion-button color="primary" fill="solid" @click="InfoPopup">
            <ion-icon :icon="infoIcon"></ion-icon>
          </ion-button>
        </ion-buttons>
      </ion-toolbar>
    </ion-header>

    <ion-content :fullscreen="true">
      <ion-header class="ion-no-border ion-padding-top ion-padding-horizontal">
        <ion-grid>
          <ion-row>
            <ion-col>
              <div class="ion-text-start" id="score">
                Clicks per Second: {{ cps.toFixed(2) }}
              </div>
            </ion-col>
            <ion-col>
              <div class="ion-text-end">
                Time Left: {{ temporitzador }}
                Counter: {{ comptador }}
              </div>
            </ion-col>
          </ion-row>
        </ion-grid>
      </ion-header>

      <div id="container">
        <ion-button @click="Boto" :disabled="counterDisabled">{{ funciona ? 'Tap Me' : 'Start Game' }}</ion-button>
      </div>
    </ion-content>
  </ion-page>
</template>

<script>
import { alertController, IonButton, IonButtons, IonContent, IonGrid, IonHeader, IonIcon, IonPage, IonTitle, IonToolbar, toastController } from '@ionic/vue';
import { defineComponent } from 'vue';
import { informationCircleOutline } from "ionicons/icons";

const INITIAL_TIME = 5;

export default defineComponent({
  name: 'ComptadorApp',
  components: {
    IonButton,
    IonButtons,
    IonContent,
    IonHeader,
    IonIcon,
    IonPage,
    IonTitle,
    IonToolbar,
    IonGrid
  },
  setup () {
    return {
      infoIcon: informationCircleOutline,
      counterDisabled: false,
    }
  },
  data () {
    return {
      comptador: 0,
      temporitzador: INITIAL_TIME,
      funciona: false,
      cps: 0
    }
  },
  watch: {
    temporitzador: function(newTime) {
      if (newTime <= 0) {
        this.funciona = false;
        clearInterval(this.countdownTimer);
        clearInterval(this.clickCountTimer);
        this.Resultat();
        this.comptador = 0;
      }
    }
  },
  methods: {
    async InfoPopup() {
      const alert = await alertController.create({
        header: 'App Comptador',
        subHeader: 'Creado por Eric Ortega Gisbert',
        message: 'Puedes encontrar el código fuente en: <a href="https://github.com/EricOrtegaGi/Ionic">https://github.com/EricOrtegaGi/Ionic</a>',
        buttons: ['OK'],
      });
      await alert.present();
    },
    Boto () {
      if (!this.funciona) {
        this.startGame();
      } else {
        this.increment();
      }
    },
    startGame () {
      this.init();
      this.funciona = true;
      this.countdownTimer = setInterval(() => {
        if (this.temporitzador > 0) {
          this.temporitzador--;
          this.cps();
        } else {
          this.endGame();
        }
      }, 1000);
    },
    increment () {
      this.comptador++;
    },
    endGame () {
      clearInterval(this.countdownTimer);
      clearInterval(this.clickCountTimer);
      this.Resultat();
      this.reset();
    },
    reset () {
      this.comptador = 0;
      this.temporitzador = INITIAL_TIME;
      this.funciona = false;
      this.cps = 0;
    },
    cps () {
      this.cps = this.comptador / (INITIAL_TIME - this.temporitzador);
    },
    init () {
      this.clickCountTimer = setInterval(() => {
        this.cps();
      }, 1000);
    },
    async Resultat () {
      const message = `Game Over! Counter: ${this.comptador}`;
      console.log(message);
      // Show toast message
      const toast = await toastController.create({
        color: 'dark',
        duration: 2000,
        message,
        showCloseButton: true
      });

      await toast.present();
    }
  }
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
