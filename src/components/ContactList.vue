<script setup>
defineProps({
  contacts: { type: Array, default: () => [] },
});
const activeIndex = defineModel("activeIndex", { type: Number, default: -1 });
</script>

<template>
  <ul class="list-group">
    <li
      class="list-group-item"
      v-for="(contact, index) in contacts"
      :key="contact._id"
      :class="{ active: index === activeIndex }"
      @click="activeIndex = index"
    >
      {{ contact.name }}
    </li>
  </ul>
</template>