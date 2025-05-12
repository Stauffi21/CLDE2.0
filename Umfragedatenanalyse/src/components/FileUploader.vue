<template>
  <div class="bg-white p-6 rounded-lg shadow-md max-w-xl mx-auto mt-10">
    <h2 class="text-xl font-semibold text-gray-800 mb-4">CSV-Datei hochladen</h2>

    <form @submit.prevent="handleFileUpload" class="space-y-4">
      <input
        type="file"
        accept=".csv"
        @change="onFileChange"
        class="block w-full text-sm text-gray-700 border rounded px-3 py-2"
      />

      <button
        type="submit"
        class="bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-700 w-full disabled:opacity-50"
        :disabled="!selectedFile || uploading"
      >
        {{ uploading ? 'Lade hoch...' : 'Datei hochladen' }}
      </button>
    </form>

    <div v-if="uploadSuccess" class="mt-4 text-green-700">
      Upload erfolgreich!<br />
      <strong>UUID:</strong> {{ result.uuid }}<br />
      <strong>Ergebnis:</strong> <a :href="result.resultUrl" class="underline text-blue-600" target="_blank">result.json</a><br />
      <strong>Diagramm:</strong> <a :href="result.visualUrl" class="underline text-blue-600" target="_blank">visual.png</a>
    </div>

    <div v-if="uploadError" class="mt-4 text-red-600">
      Fehler beim Upload. Bitte erneut versuchen.
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import axios from 'axios'

// Datei & Uploadstatus
const selectedFile = ref(null)
const uploading = ref(false)
const uploadSuccess = ref(false)
const uploadError = ref(false)
const result = ref({})

// Datei auswählen
function onFileChange(event) {
  selectedFile.value = event.target.files[0]
}

// Datei an API senden
async function handleFileUpload() {
  if (!selectedFile.value) return

  uploading.value = true
  uploadSuccess.value = false
  uploadError.value = false
  result.value = {}

  const formData = new FormData()
  formData.append('file', selectedFile.value)

  try {
    const response = await axios.post(
      'https://8n7fzhi5r2.execute-api.us-east-1.amazonaws.com/dev/upload',
      formData,
      {
        headers: {
          'Content-Type': 'multipart/form-data'
        }
      }
    )

    result.value = response.data
    uploadSuccess.value = true
  } catch (error) {
    console.error('Fehler beim Hochladen:', error)
    uploadError.value = true
  } finally {
    uploading.value = false
  }
}
</script>