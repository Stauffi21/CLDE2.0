<template>
  <div class="p-8">
    <h1 class="text-3xl font-bold">Daten hochladen</h1>
    <input type="file" accept=".csv" @change="handleFileUpload" class="mt-4" />
  </div>
</template>

<script setup>
const handleFileUpload = (event) => {
  const file = event.target.files[0]
  if (file) {
    console.log('Datei hochladen:', file.name)
    // Implementiere hier den Upload-Logik
  }
}
</script>

<style scoped>
/* Optionale Styles */
</style>