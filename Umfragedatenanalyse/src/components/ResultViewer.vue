<template>
  <div v-if="data" class="mt-10 flex flex-col items-center space-y-16">
    <!-- Mittelwerte -->
    <div class="w-full max-w-xl">
      <h3 class="text-2xl font-semibold mb-6 text-center">Mittelwerte</h3>
      <div class="overflow-x-auto">
        <table class="text-sm border border-gray-300 text-left w-full">
          <thead class="bg-gray-100">
            <tr>
              <th class="p-2 border-b">Spalte</th>
              <th class="p-2 border-b">Wert</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(value, key) in data.mean" :key="key" class="hover:bg-gray-50">
              <td class="p-2 border-b">{{ key }}</td>
              <td class="p-2 border-b">{{ value }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

    <!-- Dropdown & Visualisierung -->
    <div v-if="columnOptions.length" class="w-full max-w-xl text-center">
      <label class="block text-base font-medium mb-3">Spalte wählen für Visualisierung:</label>
      <select v-model="selectedColumn" class="border rounded px-3 py-2 w-full mb-6">
        <option v-for="col in columnOptions" :key="col" :value="col">{{ col }}</option>
      </select>

      <div v-if="selectedColumn && currentVisualUrl" class="mt-4">
        <h3 class="text-lg font-semibold mb-2">Visualisierung: {{ selectedColumn }}</h3>
        <img :src="currentVisualUrl" alt="Diagramm" class="rounded border mx-auto max-h-80" />
        <a
          :href="currentVisualUrl"
          download="diagramm.png"
          class="text-blue-600 hover:underline text-sm mt-3 block"
        >
          Diagramm herunterladen
        </a>
      </div>
    </div>

    <!-- Häufigkeiten -->
    <div class="w-full max-w-xl">
      <h3 class="text-2xl font-semibold mb-6 text-center">Häufigkeiten</h3>
      <div
        v-for="(entries, column) in data.frequencies"
        :key="column"
        class="mb-14"
      >
        <h4 class="text-md font-semibold text-gray-800 mb-3">{{ column }}</h4>
        <div class="overflow-x-auto">
          <table class="text-sm border border-gray-300 text-left w-full">
            <thead class="bg-gray-100">
              <tr>
                <th class="p-2 border-b">Wert</th>
                <th class="p-2 border-b text-right">Anzahl</th>
                <th class="p-2 border-b text-right">Prozent</th>
              </tr>
            </thead>
            <tbody>
              <tr
                v-for="(stats, value) in entries"
                :key="value"
                class="hover:bg-gray-50"
              >
                <td class="p-2 border-b">{{ value }}</td>
                <td class="p-2 border-b text-right">{{ stats.count }}</td>
                <td class="p-2 border-b text-right">{{ stats.percent }}%</td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>

  <div v-else-if="loading" class="text-gray-500 mt-8 text-center">Lade Analyse...</div>
  <div v-else class="text-red-600 mt-8 text-center">Fehler beim Laden der Analyse.</div>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue'
import axios from 'axios'

const props = defineProps({
  url: String,
  visualUrls: Object
})

const data = ref(null)
const loading = ref(true)
const selectedColumn = ref(null)

const columnOptions = computed(() =>
  data.value?.frequencies ? Object.keys(data.value.frequencies) : []
)

const currentVisualUrl = computed(() =>
  selectedColumn.value && props.visualUrls
    ? props.visualUrls[selectedColumn.value]
    : null
)

onMounted(async () => {
  try {
    const res = await axios.get(props.url)
    data.value = res.data
    selectedColumn.value = Object.keys(res.data.frequencies || {})[0] || null
  } catch (e) {
    console.error("Analyse konnte nicht geladen werden:", e)
  } finally {
    loading.value = false
  }
})
</script>
