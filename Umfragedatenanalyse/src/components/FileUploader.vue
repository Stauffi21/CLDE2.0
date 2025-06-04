<template>
  <div class="max-w-2xl mx-auto text-center mt-10 bg-white p-6 rounded shadow">
    <h2 class="text-xl font-bold mb-4">Datei hochladen</h2>

    <form @submit.prevent="handleFileUpload" class="space-y-4">
      <input type="file" accept=".csv" @change="onFileChange" />
      <button
        type="submit"
        class="bg-blue-600 text-white px-4 py-2 rounded"
        :disabled="uploading || !selectedFile"
      >
        {{ uploading ? 'Wird hochgeladen...' : 'Hochladen' }}
      </button>
    </form>

    <div v-if="uploading" class="text-gray-500 mt-4">⏳ Upload läuft...</div>
    <div v-if="polling" class="text-gray-500 mt-2">⌛ Warte auf Analyse...</div>
    <div v-if="uploadError" class="text-red-600 mt-4">❌ Fehler beim Upload</div>
    <div v-if="uploadTimeout" class="text-yellow-600 mt-4">⚠️ Analyse hat zu lange gedauert.</div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import axios from 'axios'

const emit = defineEmits(['upload-complete'])

const selectedFile = ref(null)
const uploading = ref(false)
const polling = ref(false)
const uploadError = ref(false)
const uploadTimeout = ref(false)

const API_KEY = import.meta.env.VITE_API_KEY

function onFileChange(e) {
  selectedFile.value = e.target.files[0]
}

async function postWithRetry(url, formData, retries = 1, apiKey = null) {
  try {
    const headers = {
      'Content-Type': 'multipart/form-data'
    }

    if (apiKey) {
      headers['x-api-key'] = apiKey
    }

    return await axios.post(url, formData, { headers })

  } catch (err) {
    const status = err.response?.status || 'unbekannt'
    console.warn(`⚠️ Upload-Fehler (Status ${status}):`, err.message)

    if (retries > 0 && (status === 502 || !err.response)) {
      await new Promise(r => setTimeout(r, 1500))
      return await postWithRetry(url, formData, retries - 1, apiKey)
    }

    throw err
  }
}

async function handleFileUpload() {
  if (!selectedFile.value) return

  uploading.value = true
  polling.value = false
  uploadError.value = false
  uploadTimeout.value = false

  const formData = new FormData()
  formData.append('file', selectedFile.value)

  try {
    const response = await postWithRetry(
      'https://8n7fzhi5r2.execute-api.us-east-1.amazonaws.com/dev/upload',
      formData,
      1,
      API_KEY
    )

    const { resultUrl, visualUrls } = response.data

    if (!resultUrl || !visualUrls || Object.keys(visualUrls).length === 0) {
      throw new Error("Backend-Antwort unvollständig oder fehlerhaft.")
    }

    uploading.value = false
    polling.value = true

    let ready = false
    for (let i = 0; i < 30; i++) {
      try {
        await axios.get(resultUrl)
        ready = true
        break
      } catch {
        await new Promise(resolve => setTimeout(resolve, 1000))
      }
    }

    polling.value = false

    if (!ready) {
      uploadTimeout.value = true
      return
    }

    emit('upload-complete', { resultUrl, visualUrls })

  } catch (err) {
    uploadError.value = err.message || true
    uploading.value = false
    polling.value = false
  }
}
</script>