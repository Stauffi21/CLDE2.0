<template>
    <nav class="bg-blue-600 text-white p-4">
      <div class="container mx-auto flex justify-between">
        <div class="font-bold text-lg">
          Umfrage Analyzer
        </div>
        <div class="space-x-4">
          <router-link to="/" class="hover:underline">Home</router-link>
          <router-link to="/upload" class="hover:underline">Upload</router-link>
          <router-link to="/data" class="hover:underline">Daten</router-link>
          <router-link to="/visualization" class="hover:underline">Visualisierung</router-link>
        </div>
      </div>
    </nav>
</template>