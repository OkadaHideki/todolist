

<template>
  <div>一覧</div>
  <div>{{ deadline }}</div>
  <div>{{ message }}</div>
</template>

<script setup>
const props = defineProps({
  deadline: String,
  message: String
})

</script>
