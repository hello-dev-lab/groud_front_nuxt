<template>
  <div class="flex min-h-screen">
    <Sidebar />

    <div class="flex-1 p-6 bg-gray-100">
      <div class="bg-gray-700 text-white p-6 rounded mb-6 flex justify-between items-center">
        <h1 class="text-2xl font-bold">Add Category</h1>
        <RouterLink to="/category" class="text-sm text-white underline hover:text-orange-300">← Back</RouterLink>
      </div>

      <div class="max-w-lg mx-auto bg-white rounded-lg shadow-md p-6">
        <form @submit.prevent="handleSubmit">
          <div class="mb-4">
            <label class="block text-gray-700 font-semibold mb-1">Category Name</label>
            <input v-model="name" type="text" placeholder="Enter category name"
              class="w-full px-4 py-2 border rounded" required />
          </div>
          <div class="mb-4">
            <label class="block text-gray-700 font-semibold mb-1">Category Image</label>
            <input type="file" @change="handleImageUpload" accept="image/*"
              class="w-full px-4 py-2 border rounded" />
          </div>
          <div class="flex justify-end">
            <button type="submit" class="bg-blue-500 text-white px-6 py-2 rounded hover:bg-blue-600" :disabled="isUploading">
              {{ isUploading ? 'Uploading...' : 'Save' }}
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import axios from 'axios'
import Sidebar from '~/components/sidebar.vue'

const router = useRouter()
const name = ref('')
const image = ref('')
const isUploading = ref(false)

const base_url = 'http://192.168.69.246:4000'


const token = localStorage.getItem('token')
const adminId = localStorage.getItem('firstName')


onMounted(() => {
  if (!token || !adminId) {
    alert('You must be logged in.')
    router.push('/login')
  }
})

async function handleSubmit() {
  if (!name.value || !image.value) {
    alert('Please enter all required fields.')
    return
  }

  try {
    await axios.post(
      `${base_url}/category`,
      {
        categoryName: name.value,
        image_url: image.value,
        createBy: adminId,
        updateBy: adminId
      },
      {
        headers: {
          'Content-Type': 'application/json',
          Authorization: `Bearer ${token}`
        }
      }
    )

    alert(`Category "${name.value}" created successfully!`)
    router.push('/category')
  } catch (error: any) {
    console.error('Create category error:', error.response?.data || error.message)
    alert('Failed to create category.')
  }
}

async function handleImageUpload(event: Event) {
  const target = event.target as HTMLInputElement
  const file = target.files?.[0]
  if (!file) return

  isUploading.value = true

  try {
    const formData = new FormData()
    formData.append('file', file)

    const response = await axios.post(`${base_url}/upload/upload-multiple-files`, formData, {
      headers: {
        'Content-Type': 'multipart/form-data'
      }
    })

    image.value = response.data.fileNames[0]
  } catch (error) {
    console.error('Upload failed:', error)
    alert('Image upload failed.')
  } finally {
    isUploading.value = false
  }
}
</script>
