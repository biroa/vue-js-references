<script setup>
import {ref} from "vue";
import Quiz from "./Quiz.vue";


</script>
<template>
  <div class="p-2">
    <div class="flex flex-row">
      <div class="basis-1/4"></div>
      <div class="basis-1/2 border-2 border-slate-300 rounded-md">
        <form class="border-red-950 my-1">
          <quiz></quiz>
        </form>
      </div>
      <div class="basis-1/4"></div>
    </div>
  </div>
</template>
