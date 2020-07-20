<template>
  <v-card>
    <v-tabs @change="changeCurrentTimer" v-model="currentTimer" grow>
      <v-tab v-for="timer in timers" :key="timer.name">{{ timer.name }}</v-tab>
    </v-tabs>
    <v-card class="pa-4 flex-column d-flex align-center">
      <h1 class="time">{{ displayMinutes }}:{{ displaySeconds }}</h1>
      <div class="pb-8">
        <v-btn @click="start" color="primary" class="ma-2">
          <v-icon left>mdi-play</v-icon>Start
        </v-btn>
        <v-btn @click="stop" color="error" class="ma-2">
          <v-icon left>mdi-stop</v-icon>Stop
        </v-btn>
        <v-btn @click="reset(timers[currentTimer].minutes)" :disabled="isRunning" class="ma-2">
          <v-icon left>mdi-restart</v-icon>Reset
        </v-btn>
      </div>
    </v-card>
    <UserSettings :dialog="dialog" :closeDialog="closeDialog" :timers="timers" :save="save" />
  </v-card>
</template>

<script>
import UserSettings from "./UserSettings";
export default {
  components: { UserSettings },
  props: {
    dialog: {
      type: Boolean,
      required: true
    },
    closeDialog: {
      type: Function,
      required: true
    }
  },
  data() {
    return {
      isRunning: false,
      timerInstance: null,
      totalSeconds: 25 * 60,
      currentTimer: 0,
      timers: [
        { name: "Pomodoro", minutes: 25 },
        { name: "Short Breaks", minutes: 5 },
        { name: "Long Breaks", minutes: 10 }
      ]
    };
  },
  computed: {
    displayMinutes() {
      const minutes = Math.floor(this.totalSeconds / 60);
      return this.formatTime(minutes);
    },
    displaySeconds() {
      const seconds = this.totalSeconds % 60;
      return this.formatTime(seconds);
    }
  },
  methods: {
    formatTime(time) {
      if (time < 10) {
        return "0" + time;
      }
      return time.toString();
    },
    start() {
      this.stop();
      this.isRunning = true;

      this.timerInstance = setInterval(() => {
        if (this.totalSeconds <= 0) {
          this.stop();
          return;
        }
        this.totalSeconds--;
      }, 10);
    },
    stop() {
      this.isRunning = false;
      clearInterval(this.timerInstance);
    },
    reset(minutes) {
      this.stop();
      this.totalSeconds = minutes * 60;
    },
    changeCurrentTimer(num) {
      this.currentTimer = num;
      this.reset(this.timers[num].minutes);
    },
    save(updatedTimers) {
      this.timers = this.timers.map((timer, i) => {
        return { ...timer, minutes: parseInt(updatedTimers[i]) };
      });
      this.closeDialog();
    }
  }
};
</script>

<style scoped>
.time {
  font-size: 6rem;
  font-weight: 500;
  text-align: center;
  padding: 2rem;
}
</style>
