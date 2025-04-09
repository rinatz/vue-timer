<script setup lang="ts">
import { computed, ref } from "vue";
import dayjs from "dayjs";
import duration from "dayjs/plugin/duration";

dayjs.extend(duration);

const timer = ref(dayjs.duration(0, "second"));

const hour = ref(0);
const minute = ref(0);
const second = ref(0);

type Status = "stopped" | "running" | "paused";
const status = ref("stopped" as Status);

const ready = computed(() => {
  return hour.value > 0 || minute.value > 0 || second.value > 0;
});

const buttonText = computed(() => {
  if (status.value === "stopped") {
    return "開始";
  } else if (status.value === "running") {
    return "一時停止";
  } else {
    return "再開";
  }
});

const buttonColor = computed(() => {
  if (status.value === "running") {
    return "bg-pink";
  } else {
    return "bg-blue";
  }
});

function startTimer() {
  status.value = "running";

  timer.value = dayjs.duration({
    hours: hour.value,
    minutes: minute.value,
    seconds: second.value,
  });

  const interval = setInterval(() => {
    if (status.value === "running") {
      timer.value = timer.value.subtract(1, "second");
    }

    if (timer.value.asSeconds() <= 0) {
      status.value = "stopped";
      clearInterval(interval);
    }
  }, 1000);
}

function toggleTimer() {
  if (status.value === "running") {
    status.value = "paused";
  } else if (status.value === "paused") {
    status.value = "running";
  }
}

function resetTimer() {
  status.value = "stopped";
  timer.value = dayjs.duration(0, "second");
  hour.value = 0;
  minute.value = 0;
  second.value = 0;
}
</script>

<template>
  <div class="mx-10 mt-8">
    <div class="text-center text-h1">{{ timer.format("HH:mm:ss") }}</div>

    <div class="d-flex flex-column align-center mt-8">
      <v-slider v-model="hour" label="時" style="width: 512px;" :min="0" :max="99" :step="1" thumb-label>
        <template v-slot:append>
          <v-text-field v-model="hour" density="compact" style="width: 70px" type="number" hide-details single-line>
          </v-text-field>
        </template>
      </v-slider>

      <v-slider v-model="minute" label="分" style="width: 512px;" :min="0" :max="59" :step="1" thumb-label>
        <template v-slot:append>
          <v-text-field v-model="minute" density="compact" style="width: 70px" type="number" hide-details single-line>
          </v-text-field>
        </template>
      </v-slider>

      <v-slider v-model="second" label="秒" style="width: 512px;" :min="0" :max="59" :step="1" thumb-label>
        <template v-slot:append>
          <v-text-field v-model="second" density="compact" style="width: 70px" type="number" hide-details single-line>
          </v-text-field>
        </template>
      </v-slider>
    </div>

    <div class="d-flex justify-center ga-4">
      <v-btn @click="resetTimer">リセット</v-btn>
      <v-btn v-if="status === 'stopped'" :disabled="!ready" @click="startTimer" :class="buttonColor">開始</v-btn>
      <v-btn v-else @click="toggleTimer" :class="buttonColor">{{ buttonText }}</v-btn>
    </div>
  </div>
</template>
