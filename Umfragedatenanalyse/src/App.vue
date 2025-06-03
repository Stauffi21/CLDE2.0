<script setup>
import { ref, nextTick } from 'vue'
import FileUploader from './components/FileUploader.vue'
import ResultViewer from './components/ResultViewer.vue'

const result = ref({})
const componentKey = ref(0)

function handleResult(uploadResult) {
  console.log("📥 Upload abgeschlossen:", uploadResult)
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
  <div class="min-h-screen flex items-center justify-center bg-gray-50 p-6">
    <div class="w-full max-w-3xl bg-white rounded-xl shadow-md p-8 text-center">
      <h1 class="text-2xl font-bold mb-6">Umfragedaten analysieren</h1>

      <FileUploader @upload-complete="handleResult" />

      <div v-if="result.resultUrl" class="mt-8 space-y-4">
        <ResultViewer
          v-if="result.resultUrl"
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
  </div>
</template>