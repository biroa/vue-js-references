<script setup>
import CountdownHeader from "@/components/CountdownHeader.vue";
import CountdownSegment from "@/components/CountdownSegment.vue";
import {computed, onMounted, ref} from "vue";
import moment from "moment";

/** -==Time Functions Start==- **/

/**
 * @description Convert timestamp to days
 * @param distance - timestamp difference
 * @returns {number}
 */
const getDays = (distance) => {
  return Math.floor(distance / (1000 * 60 * 60 * 24));
}

/**
 * @description Convert timestamp to Hours
 * @param distance - timestamp difference
 * @returns {number}
 */
const getHours = (distance) => {
  return Math.floor((distance % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
}

/**
 * @description Convert timestamp to Minutes
 * @param distance - timestamp difference
 * @returns {number}
 */
const getMinutes = (distance) => {
  return Math.floor((distance % (1000 * 60 * 60)) / (1000 * 60));
}

/**
 * @description Convert timestamp to Seconds
 *
 * @param distance - timestamp difference
 * @returns {number}
 */
const getSeconds = (distance) => {
  return Math.floor((distance % (1000 * 60)) / 1000);
}

/** -==Time Functions Over==- **/

/**
 * Get the current date in different time formats
 * @type {ComputedRef<{date: string, timestamp: number}>}
 */
const currentDate = computed(() => {
  let currDate = new Date();
  return {
    date: moment(currDate).format('YYYY-MM-DD HH:MM:SS'),
    timestamp: currDate.getTime()
  }
});

/**
 * Get the new year's date in different time formats
 * @type {ComputedRef<{date: string, timestamp: number}>}
 */
const newYearDate = computed(()=>{
  let nextYear = (new Date().getFullYear() + 1);
  let newYear = new Date(nextYear,0,1,0,0,0);
  return {
    date: moment(newYear).format('YYYY-MM-DD HH:mm:ss'),
    timestamp: newYear.getTime()
  }
})

// Count the timestamp difference between the two dates
const distance = ref(newYearDate.value.timestamp - currentDate.value.timestamp);

const days = ref(getDays(distance.value));
const hours = ref(getHours(distance.value));
const minutes = ref(getMinutes(distance.value));
const seconds = ref(getSeconds(distance.value));


onMounted(()=>{
  setInterval(() => {
    let distance = newYearDate.value.timestamp - new Date().getTime();
    days.value = getDays(distance);
    hours.value = getHours(distance);
    minutes.value = getMinutes(distance);
    seconds.value = getSeconds(distance);
  }, 1000);
})

</script>
<template>
  <div class="app-wrapper">
    <div class="countdown-box">
      <CountdownHeader />
      <main class="flex justify-center">
        <CountdownSegment data-test="days" :key="days" :label="`days`" :number="days" />
        <CountdownSegment data-test="hours" :key="hours" :label="`hours`" :number="hours" />
        <CountdownSegment data-test="minutes" :key="minutes" :label="`minutes`" :number="minutes" />
        <CountdownSegment data-test="seconds" :key="seconds" :label="`seconds`" :number="seconds" />
      </main>
    </div>
  </div>
</template>

<style scoped>
.app-wrapper {
  @apply flex items-center justify-center w-full h-full p-10;
}
.countdown-box {
  @apply shadow-md relative bg-white p-5 rounded-lg border-gray-100 border-[1px];
}
body {
  @apply bg-gray-100;
}
</style>
