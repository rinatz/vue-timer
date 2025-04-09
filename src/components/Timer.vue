<script setup lang="ts">
import { computed, ref } from "vue";
import dayjs from "dayjs";

const values = Array.from({ length: 100 }, (_, i) => {
  return { value: i, label: String(i).padStart(2, "0") }
});

const timer = ref(dayjs().startOf("date"));

function setHour(event: Event) {
  const target = event.target as HTMLSelectElement;
  timer.value = timer.value.set("hour", parseInt(target.value));
}

function setMinute(event: Event) {
  const target = event.target as HTMLSelectElement;
  timer.value = timer.value.set("minute", parseInt(target.value));
}

function setSecond(event: Event) {
  const target = event.target as HTMLSelectElement;
  timer.value = timer.value.set("second", parseInt(target.value));
}

type Status = "stopped" | "running" | "paused";

const status = ref("stopped" as Status);

const message = computed(() => {
  if (status.value === "stopped") {
    return "開始";
  } else if (status.value === "running") {
    return "一時停止";
  } else {
    return "再開";
  }
});

function startTimer() {
  status.value = "running";

  const interval = setInterval(() => {
    if (status.value === "running") {
      timer.value = timer.value.subtract(1, "second");
    }

    if (timer.value.format("HH:mm:ss") === "00:00:00") {
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
</script>

<template>
  <select @change="setHour">
    <option v-for="value in values" :key="value.value" :value="value.label">{{ value.label }}</option>
  </select>
  :
  <select @change="setMinute">
    <option v-for="value in values.slice(0, 60)" :key="value.value" :value="value.label">{{ value.label }}
    </option>
  </select>
  :
  <select @change="setSecond">
    <option v-for="value in values.slice(0, 60)" :key="value.value" :value="value.label">{{ value.label }}
    </option>
  </select>

  <p>{{ timer.format("HH:mm:ss") }}</p>
  <button v-if="status === 'stopped'" @click="startTimer">開始</button>
  <button v-else @click="toggleTimer">{{ message }}</button>
</template>

<style scoped></style>
