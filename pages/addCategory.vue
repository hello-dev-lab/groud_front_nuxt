<template>
  <div class="flex min-h-screen">
    <!-- Sidebar -->
    <Sidebar />

    <!-- Main Content -->
    <div class="flex-1 p-6 bg-gray-100">
      <!-- Page Header -->
      <div class="bg-gray-700 text-white p-6 rounded mb-6 flex justify-between items-center">
        <h1 class="text-2xl font-bold">Add Category</h1>
        <RouterLink
          to="/category"
          class="text-sm text-white underline hover:text-orange-300"
        >
          ← Back to Category
        </RouterLink>
      </div>

      <!-- Form Card -->
      <div class="max-w-lg mx-auto bg-white rounded-lg shadow-md p-6">
        <form @submit.prevent="handleSubmit">
          <div class="mb-4">
            <label for="name" class="block text-gray-700 font-semibold mb-1">Category Name</label>
            <input
              id="name"
              v-model="name"
              type="text"
              placeholder="Enter category name"
              class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-400 focus:outline-none"
              required
            />
          </div>
          <div class="mb-4">
            <label for="image" class="block text-gray-700 font-semibold mb-1">Category Image</label>
            <input
              type="file"
              id="image"
              @change="handleImageUpload"
              accept="image/*"
              class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-400 focus:outline-none"
            />
          </div>

          <div class="flex justify-end">
            <button
              type="submit"
              class="bg-blue-500 hover:bg-blue-600 text-white px-6 py-2 rounded-lg transition"
              :disabled="isUploading"
            >
              {{ isUploading ? 'Uploading...' : 'Save' }}
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import axios from 'axios'
import Sidebar from '~/components/sidebar.vue'

const router = useRouter()
const name = ref('')
const image = ref('')
const isUploading = ref(false)

const base_url = 'http://192.168.69.246:4000'

async function handleSubmit() {
  if (!name.value) {
    alert('Please enter category name.')
    return
  }
  if (!image.value) {
    alert('Please upload a category image.')
    return
  }

  try {
    console.log('Submitting category:', {
      categoryName: name.value,
      image_url: image.value
    })

    await axios.post(`${base_url}/category`, {
      categoryName: name.value,
      image_url: image.value,
    })

    alert(`Create category "${name.value}" successfully!`)
    router.push('/category')
  } catch (error: any) {
    console.error('Failed to add category:', error.response?.data || error.message)
    alert('Something went wrong while adding the category.')
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

    const response = await axios.post(
      `${base_url}/upload/upload-multiple-files`,
      formData,
      {
        headers: {
          'Content-Type': 'multipart/form-data',
        },
      }
    )

    console.log('Upload response:', response.data.fileNames[0])

    image.value = response.data.fileNames[0]

    console.log('Image filename:', image.value)

    if (!image.value) {
      alert('Image upload succeeded but no filename returned.')
      return
    }

    console.log('Image filename:', image.value)
    
  } catch (error) {
    console.error('Failed to upload image:', error)
    alert('Image upload failed.')
  } finally {
    isUploading.value = false
  }
}
</script>
