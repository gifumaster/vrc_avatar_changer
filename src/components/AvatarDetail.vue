<template>
  <div class="area" :style="styles" :title="name"></div>
</template>

<script setup>
import { computed } from "vue";

const props = defineProps({
  name: { type: String, required: true },
  thumbnailImageUrl: { type: String, required: true },
});

const name = computed(() => props.name);
const styles = computed(() => {
  return "background-image: url('" + props.thumbnailImageUrl + "')";
});
</script>

<style>
.area {
  margin: 1px;
  padding: 0;
  width: 192px;
  height: 144px;
  background-position: center;
  background-size: 100%;
  border-radius: 5px;
  transition: background-size 0.2s;
}

.area:hover {
  background-position: center;
  background-size: 120%;
  box-sizing: border-box;
  border: 3px solid red;
  transition: background-size 0.2s;
}
</style>
