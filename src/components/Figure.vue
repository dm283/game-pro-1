<script setup>
import { defineProps } from 'vue';

defineProps({
  figClr: String,
})

</script>

<template>
  <div :class=figClr>
    <i class="pi pi-circle-fill" style="font-size: 2rem"></i>  
  </div>
</template>
