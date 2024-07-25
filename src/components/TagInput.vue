<template>
  <input v-show="false" type="hidden" name="tags" :value="tagsJson" />
  <vue3-tags-input
    v-show="false"
    :tags="tags"
    :placeholder="title"
    @on-tags-changed="updateTags"
  />

  <div style="margin-top: 5px" class="d-flex flex-wrap">
    <template v-for="shortcut in shortcuts" :key="shortcut">
      <v-btn
        class="mt-1 mr-1"
        @click="() => add(shortcut.searchName)"
        :color="selected(shortcut.searchName)"
        >{{ shortcut.name }}</v-btn
      >
    </template>
  </div>
</template>

<script setup>
import { computed, ref } from "vue";
import Vue3TagsInput from "vue3-tags-input";

const shortcuts = ref([]);

const loadTags = () => {
  const savedTags = localStorage.getItem("tags");
  shortcuts.value = JSON.parse(savedTags);
};

loadTags();

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

const selected = computed(() => (arg) => {
  return Object.values(tags.value).includes(arg) ? "primary" : "";
});

const tagsJson = computed(() => {
  return JSON.stringify(tags.value);
});

const updateTags = (newTags) => {
  console.info(tags.value, newTags);
  tags.value = newTags;
};
</script>
