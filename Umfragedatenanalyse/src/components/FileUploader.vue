<template>
    <div class="bg-white p-6 rounded-lg shadow-md">
      <form @submit.prevent="handleFileUpload">
        <input
          type="file"
          accept=".csv"
          @change="onFileChange"
          class="block w-full text-sm text-gray-600 mb-4"
        />
        <button
          type="submit"
          class="bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-700"
          :disabled="!selectedFile || uploading"
        >
          {{ uploading ? 'Hochladen...' : 'Datei hochladen' }}
        </button>
      </form>
  
      <div v-if="uploadSuccess" class="text-green-600 mt-4">
        Upload erfolgreich!
      </div>
      <div v-if="uploadError" class="text-red-600 mt-4">
        Fehler beim Upload.
      </div>
    </div>
  </template>
  
<script setup>
  import { ref } from 'vue'
  import axios from 'axios'
  
  const selectedFile = ref(null)
  const uploading = ref(false)
  const uploadSuccess = ref(false)
  const uploadError = ref(false)
  
  const emit = defineEmits(['upload-success'])
  
  function onFileChange(event) {
    selectedFile.value = event.target.files[0]
  }
  
  async function handleFileUpload() {
    if (!selectedFile.value) return
  
    uploading.value = true
    uploadSuccess.value = false
    uploadError.value = false
  
    const formData = new FormData()
    formData.append('file', selectedFile.value)
  
    try {
      const response = await axios.post('https://your-api-endpoint.com/upload', formData, {
        headers: {
          'Content-Type': 'multipart/form-data'
        }
      })
      uploadSuccess.value = true
      emit('upload-success', response.data)
    } catch (error) {
      console.error(error)
      uploadError.value = true
    } finally {
      uploading.value = false
    }
  }
</script>