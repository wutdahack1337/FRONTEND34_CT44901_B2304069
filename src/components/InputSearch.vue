<script setup>
const model = defineModel({ type: String, default: "" });
const emit = defineEmits(["submit"]);
</script>

<template>
  <div class="input-group">
    <input type="text" class="form-control" placeholder="Nhập thông tin cần tìm" v-model="model" @keyup.enter="emit('submit')" />
    <div class="input-group-append">
      <button class="btn btn-outline-secondary" type="button" @click="emit('submit')">
        <i class="fas fa-search"></i> Tìm kiếm
      </button>
    </div>
  </div>
</template>