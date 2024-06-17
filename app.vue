<script setup>
  useSeoMeta({
  title: 'Marvel Characters',
  ogTitle: 'Marvel Characters',
  description: 'Its w website to show some Moarvel Characters details',
  ogDescription: 'Its w website to show some Moarvel Characters details.',
  ogImage: '/assets/images/Marvel.png',
})
</script>

<template>
  <div class="bg-red">
    <NuxtLoadingIndicator />

    <NuxtLayout />
    <NuxtPage />

  </div>
  
</template>

<style></style>
