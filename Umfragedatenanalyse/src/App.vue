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

    <!-- MAIN -->
    <main class="flex-grow flex items-center justify-center p-6">
      <div class="w-full max-w-3xl bg-white rounded-xl shadow-md p-8 text-center">
        <h1 class="text-3xl font-extrabold text-gray-800 tracking-tight mb-2">📊 Umfragedaten analysieren</h1>
        <p class="text-base text-gray-600 max-w-xl mx-auto">Lade deine <span class="font-medium">CSV-Datei</span> hoch. Achte darauf, dass das Trennzeichen ein <code class="bg-gray-100 px-1 py-0.5 rounded text-sm text-gray-800">;</code> ist.</p>

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
  </div>
</template>