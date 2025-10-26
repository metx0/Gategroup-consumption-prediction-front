<template>
  <div class="flex flex-row gap-x-6 px-6 py-5 border-b-2 border-gray-300 justify-between">
    <div
      class="flex flex-col justify-center items-center gap-y-6 rounded-lg shadow-md border border-gray-200 p-6 h-[50vh]"
    >
      <h2 class="text-xl font-semibold">Upload Airline Specification file</h2>
      <FileUpload
        ref="fileupload"
        mode="basic"
        name="demo[]"
        accept=".csv"
        :customUpload="true"
        @select="onFileSelect"
      />
      <Button class="w-full" label="Upload and predict" @click="upload" severity="contrast" />
    </div>
    <div class="w-[70%] h-[50vh]">
      <DataTable
        size="small"
        scroll-height="50vh"
        scrollable
        striped-rows
        :value="tableData"
        table-style="width: 100%"
      >
        <template #empty>The data will appear here</template>
        <Column v-for="col of columns" :key="col.field" :field="col.field" :header="col.header">
          <template v-if="col.field === 'recommended_quantity'" #body="slotProps">
            <span class="text-green-600 font-bold">{{ slotProps.data[col.field] }}</span>
          </template>
        </Column>
      </DataTable>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useToast } from 'primevue/usetoast'
import { Button, FileUpload, DataTable, Column } from 'primevue'
import { gategroupApi } from '@/api/axios'

const emit = defineEmits(['dataReady'])
const toast = useToast()
const fileupload = ref()
const selectedFile = ref(null)

const tableData = ref()

const columns = [
  { field: 'nombre de aerolinea_GateGroup Airlines', header: 'Airline' },
  { field: 'PASSENGERS', header: 'Passengers' },
  { field: 'FECHA', header: 'Date' },
  { field: 'ORIGEN', header: 'Origin' },
  { field: 'DESTINO', header: 'Destiny' },
  { field: 'DURATION_MIN', header: 'Duration' },
  { field: 'DEPARTUTE LOCAL TIME', header: 'Departure' },
  { field: 'ARRIVAL LOCAL TIME', header: 'Arrival' },
  { field: 'ITEM CODE', header: 'Item code' },
  { field: 'item_name', header: 'Item name' },
  { field: 'CATEGORY', header: 'Category' },
  { field: 'SUPERCATEGORY', header: 'Subcategory' },
  { field: 'currency_EUR', header: 'Currency' },
  { field: 'quantity', header: 'Quantity' },
  { field: 'unit_price', header: 'Unit Price' },
  { field: 'recommended_quantity', header: 'Recomended Quantity' },
]

const parseCsv = (csvString) => {
  const lines = csvString.split('\n').filter((line) => line.trim() !== '')

  if (lines.length === 0) return { columns: [], data: [] }

  // First line = headers
  const headers = lines[0] ? lines[0].split(',').map((h) => h.trim()) : []
  const columns = headers.map((header) => ({ field: header, header }))

  // Remaining lines = rows
  const data = lines.slice(1).map((line) => {
    const values = line.split(',').map((v) => v.trim())
    const row = {}
    headers.forEach((header, index) => {
      row[header] = values[index] ?? ''
    })
    return row
  })

  return { columns, data }
}

// Store selected file
const onFileSelect = (event) => {
  selectedFile.value = event.files[0]
}

const upload = async () => {
  if (!selectedFile.value) {
    toast.add({
      severity: 'warn',
      summary: 'No file',
      detail: 'Please select a file first',
      life: 2000,
    })
    return
  }

  try {
    const formData = new FormData()
    formData.append('file', selectedFile.value)

    // Send the file to the backend
    const response = await gategroupApi.post('predict', formData, {
      headers: { 'Content-Type': 'multipart/form-data' },
    })

    console.log('Server response:', response.data)

    // This is an array with length corresponding to the number of rows in the CSV file
    const predictionResult = response.data

    const fileText = await selectedFile.value.text()
    const { data } = parseCsv(fileText)
    // console.log(data)

    // Construct the final data

    for (let i = 0; i < data.length; i++) {
      data[i].recommended_quantity = predictionResult[i]
    }

    tableData.value = data
    emit('dataReady', tableData.value)
    console.log(tableData.value)

    toast.add({
      severity: 'success',
      summary: 'Success',
      detail: 'File analyzed successfully',
      life: 2000,
    })
  } catch (error) {
    console.error(error)
    toast.add({ severity: 'error', summary: 'Upload failed', detail: error.message, life: 2000 })
  }
}
</script>

<style scoped></style>
