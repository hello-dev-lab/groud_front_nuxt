<template>
  <div class="max-w-3xl mx-auto p-6 bg-white shadow rounded-lg mt-6">
    <h2 class="text-2xl font-semibold mb-4">Add New Product</h2>

    <form @submit.prevent="handleSubmit" class="space-y-4">
      <!-- Image Upload -->
      <div>
        <label class="block text-sm font-medium mb-1">Upload Images</label>
        <input type="file" @change="handleImageUpload" accept="image/*"
          class="w-full mb-4 border rounded p-2" />
      </div>

      <div>
        <label class="block text-sm font-medium mb-1">Name</label>
        <input v-model="form.name" type="text" class="w-full border rounded p-2" required />
      </div>

      <div class="grid grid-cols-2 gap-4">
        <div>
          <label class="block text-sm font-medium mb-1">Price</label>
          <input v-model="form.price" type="number" class="w-full border rounded p-2" required />
        </div>
        <div>
          <label class="block text-sm font-medium mb-1">Original Price</label>
          <input v-model="form.originalPrice" type="number" class="w-full border rounded p-2" />
        </div>
      </div>

      <div>
        <label class="block text-sm font-medium mb-1">Description</label>
        <textarea v-model="form.description" class="w-full border rounded p-2" rows="4"></textarea>
      </div>

      <div>
        <label class="block text-sm font-medium mb-1">Category</label>
        <select v-model="form.category" class="w-full border rounded p-2" required>
          <option disabled value="">Select a category</option>
          <option v-for="cat in categories" :key="cat.id" :value="cat.categoryName">
            {{ cat.categoryName }}
          </option>
        </select>
      </div>

      <div class="flex items-center gap-4">
        <button type="submit" class="bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-700">Add Product</button>
        <button @click="goBack" type="button"
          class="bg-gray-500 text-white px-4 py-2 rounded hover:bg-gray-600">Back</button>
      </div>
    </form>

    <p v-if="errorMessage" class="text-red-600 mt-2">{{ errorMessage }}</p>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import axios from 'axios'

const base_url = 'http://192.168.69.246:4000'
const router = useRouter()

const form = ref({
  name: '',
  price: '',
  originalPrice: '',
  description: '',
  category: ''
})

const image = ref('')
const categories = ref([])
const errorMessage = ref('')

async function handleImageUpload(event) {
  const target = event.target;
  if (!(target instanceof HTMLInputElement) || !target.files?.length) return;

  const file = target.files[0];
  const formData = new FormData();
  formData.append('file', file);

  try {
    const response = await axios.post(
      `${base_url}/upload/upload-multiple-files`,
      formData,
      {
        headers: { 'Content-Type': 'multipart/form-data' }
      }
    );

    const uploadedFileName = response.data.fileNames?.[0];
    if (uploadedFileName) {
      image.value = uploadedFileName;
    } else {
      alert('Image upload failed.');
    }

  } catch (error) {
    console.error('Image upload error:', error);
    alert('Image upload failed.');
  }
}

const handleSubmit = async () => {
  if (!image.value) {
    alert('Please upload an image.');
    return;
  }

  const token = localStorage.getItem('token');
  const AdminId = localStorage.getItem('firstName');

  const payload = {
    name: form.value.name,
    price: form.value.price,
    original_price: form.value.originalPrice,
    description: form.value.description,
    category: form.value.category,
    image_url: image.value,
    createBy: AdminId,
    updateBy: AdminId 
  };

  try {
    const response = await fetch(`${base_url}/product`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        Authorization: `Bearer ${token}`
      },
      body: JSON.stringify(payload)
    });
    
    if (response.ok) {
      alert('Product added successfully!');
      router.push('/products');
    } else {
      const errorData = await response.json();
      alert(errorData.message || 'Failed to add product.');
    }

  } catch (err) {
    console.error('Submit error:', err);
    errorMessage.value = 'Failed to add product.';
  }
}

onMounted(async () => {
  try {
    const res = await fetch(`${base_url}/category/getAll`)
    const data = await res.json()
    categories.value = data.categories
  } catch (err) {
    errorMessage.value = 'Failed to load categories'
  }
})

const goBack = () => {
  router.push('/products')
}
</script>
