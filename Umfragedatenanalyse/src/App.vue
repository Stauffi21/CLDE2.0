<script setup>
import { ref, nextTick } from 'vue'
import FileUploader from './components/FileUploader.vue'
import ResultViewer from './components/ResultViewer.vue'

const result = ref({})
const componentKey = ref(0)

function handleResult(uploadResult) {
  //console.log("📥 Upload abgeschlossen:", uploadResult)
  result.value = {}
  nextTick(() => {
    result.value = uploadResult
    componentKey.value++
  })
}

function getUuidFromUrl(url) {
  return url.split('/').pop().replace('.json', '')
}
</script>

<template>
  <div class="min-h-screen flex flex-col bg-gray-50">

    <!-- HEADER -->
    <header class="bg-blue-600 text-white p-4 shadow-md">
      <div class="max-w-6xl mx-auto text-lg font-semibold">
        📊 Umfrage Analyzer – Analyse leicht gemacht
      </div>
    </header>

    <!-- MAIN -->
    <main class="flex-grow flex items-center justify-center p-6">
      <div class="w-full max-w-3xl bg-white rounded-xl shadow-md p-8 text-center">
        <h1 class="text-2xl font-bold mb-6">Umfragedaten analysieren</h1>

        <FileUploader @upload-complete="handleResult" />

        <div v-if="result.resultUrl" class="mt-8 space-y-4">
          <ResultViewer
            :key="componentKey"
            :url="result.resultUrl"
            :visualUrls="result.visualUrls"
          />
          <div class="text-center">
            <a
              :href="result.resultUrl"
              download="analyse.json"
              class="text-blue-600 hover:underline text-sm"
            >
              Analyse als JSON herunterladen
            </a>
          </div>
        </div>
      </div>
    </main>

    <!-- FOOTER -->
    <footer class="bg-gray-100 text-center text-sm text-gray-600 py-4 border-t">
      © {{ new Date().getFullYear() }} Umfrage Analyzer - Erstellt im Modul CLDE mit der AWS Cloud und Vue.js
    </footer>
  </div>
</template>