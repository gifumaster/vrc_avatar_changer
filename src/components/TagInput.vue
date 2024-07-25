<template>
  <input type="hidden" name="tags" :value="tagsJson" />
  <vue3-tags-input
    :tags="tags"
    :placeholder="title"
    @on-tags-changed="updateTags"
  />

  <div style="margin-top: 5px" class="d-flex flex-wrap">
    <template v-for="(shortcut, index) in shortcuts" :key="shortcut">
      <v-btn class="mt-1 mr-1" @click="() => add(shortcut)" density="compact">{{
        index
      }}</v-btn>
    </template>
  </div>
</template>

<script setup>
import { computed, ref } from "vue";
import Vue3TagsInput from "vue3-tags-input";
import * as fs from "fs";

const shortcuts = ref([]);

fs.readFile("./resources/tag.json", "utf8", (err, data) => {
  if (err) {
    return;
  }
  shortcuts.value = JSON.parse(data);
});

const props = defineProps(["modelValue", "title"]);
const emit = defineEmits(["update:modelValue"]);

const tags = computed({
  get() {
    return props.modelValue;
  },
  set(newTags) {
    emit("update:modelValue", newTags);
  },
});

const add = (value) => {
  const tempTags = [...tags.value];
  const index = tempTags.indexOf(value);

  if (index === -1) {
    emit("update:modelValue", [...tags.value, value]);
  } else {
    emit("update:modelValue", [...tags.value.filter((i) => i !== value)]);
  }
};

const tagsJson = computed(() => {
  return JSON.stringify(tags.value);
});

const updateTags = (newTags) => {
  console.info(tags.value, newTags);
  tags.value = newTags;
};
</script>
