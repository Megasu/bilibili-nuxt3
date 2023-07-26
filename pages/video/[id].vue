<script setup lang="ts">
const { params } = useRoute()

console.log('动态路由id', params.id)
</script>

<template>
  <AppHeader />
  <h2>视频页 {{ $route.params.id }}</h2>
</template>

<style scoped></style>
