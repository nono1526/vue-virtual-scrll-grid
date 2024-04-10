<template>
  <div class="card">{{ id }}</div>
</template>

<script setup>
defineProps(['id'])
</script>

<style>
.card {
  background-color: #f0f0f0;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  transition: box-shadow 0.3s;
  width: 100px;
  height: 100px;
  display: flex;
  justify-content: center;
  align-items: center;
  position: absolute;
  user-select: none;
}
</style>