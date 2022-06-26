<template>
  <img class="thumb" :src="src" alt="" />
</template>

<script setup>
const props = defineProps({
  src: String,
});
</script>

<style scoped>
.thumb {
  width: 50px;
}
</style>
