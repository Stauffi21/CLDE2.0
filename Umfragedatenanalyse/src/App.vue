<template>
  <div class="min-h-screen flex flex-col bg-gray-50">
    <Navbar />
    <div class="flex-grow container mx-auto p-4">
      <router-view />
    </div>
  </div>
</template>

<script setup>
import Navbar from '@/components/Navbar.vue'
</script>