<script setup>
import { defineProps } from 'vue';

defineProps({
    data: '',
    showD: '',
})

</script>

<template>

<div class="grid grid-cols-3 gap-2 mt-10 w-max">

  <div class="w-60 border rounded-md overflow-hidden text-green-600 text-sm text-center">
  <div class="bg-pink-400 text-white font-semibold">state</div>
  <div class="grid grid-cols-2 bg-pink-50 text-pink-500" v-for="(value, key) in data.state">
    <div class="border-t">
      {{ key }}
    </div>
    <div class="border-t">
      {{ value }}
    </div>
  </div>
</div>

<div v-show="showD" class="w-60 border rounded-md overflow-hidden text-green-600 text-sm text-center">
  <div class="bg-pink-400 text-white font-semibold">cell info</div>
  <div class="grid grid-cols-2 bg-pink-50 text-pink-500" v-for="(value, key) in data.cell">
    <div class="border-t">
      {{ key }}
    </div>
    <div class="border-t">
      {{ value }}
    </div>
  </div>
</div>

<div v-show="showD" class="w-60 border rounded-md overflow-hidden bg-pink-50 text-green-600 text-sm text-center" v-if="data.cell.figId !== ''">
  <div class="bg-pink-400 text-white font-semibold">figure info</div>
  <div class="grid grid-cols-2 bg-pink-50 text-pink-500" v-for="(value, key) in data.figure">
    <div class="border-t">
      {{ key }}
    </div>
    <div class="border-t">
      {{ value }}
    </div>
  </div>
</div>

</div>

</template>