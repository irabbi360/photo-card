<script setup lang="ts">
import CardGenerator from "./components/CardGenerator.vue"
</script>

<template>
  <main>
    <CardGenerator />
  </main>
</template>

<style>
body {
  margin: 0;
  padding: 0;
  font-family: sans-serif;
  background-color: #fafafa;
}

main {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
