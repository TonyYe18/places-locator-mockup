<script setup lang="ts">
const config = useRuntimeConfig();

useHead({
  script: [
    {
      src: `https://maps.googleapis.com/maps/api/js?key=${config.public.googleApiKey}`,
      defer: true
    }
  ]
})
</script>

<template>
  <div>
    
  </div>
  <NuxtPage />
</template>
