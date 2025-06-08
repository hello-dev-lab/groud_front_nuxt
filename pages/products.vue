<template>
  <div class="flex min-h-screen bg-gray-100">
    <Sidebar />
    <div class="flex-1">
      <!-- Header -->
      <div class="p-6 bg-gray-700 text-white flex justify-between items-center">
        <h1 class="text-2xl font-bold">Products</h1>
        <div class="flex items-center space-x-4">
          <div class="relative">
            <input
              type="search"
              placeholder="Search products"
              class="p-2 pl-8 rounded text-white border border-gray-300"
              v-model="keyword"
              @input="searchProducts"
            />
            <i class="fa-solid fa-magnifying-glass absolute left-2 top-2.5 text-gray-600"></i>
          </div>
          <i class="fa-solid fa-user text-xl"></i>
        </div>
      </div>

      <!-- Add and Delete Buttons -->
      <div class="flex justify-between items-center mr-6">
        <div class="p-4">
          <button class="bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-700">
            <nuxt-link to="/addProduct">+ Add Product</nuxt-link>
          </button>
        </div>
        <div>
          <button class="bg-red-600 text-white px-4 py-2 rounded hover:bg-red-700" @click="deleteProducts">Delete</button>
        </div>
      </div>

      <!-- Edit Form -->
      <div v-if="editingProduct" class="p-4 bg-white shadow-md m-4 rounded">
        <h2 class="text-xl font-semibold mb-4">Edit Product</h2>
        <form @submit.prevent="submitEdit">
          <div class="grid grid-cols-2 gap-4">
            <input v-model="editForm.name" placeholder="Product Name" class="border p-2 rounded" />
            <input v-model="editForm.price" type="number" placeholder="Price" class="border p-2 rounded" />
            <input v-model="editForm.original_price" type="number" placeholder="Original Price" class="border p-2 rounded" />
            <input v-model="editForm.category" placeholder="Category" class="border p-2 rounded" />
            <textarea v-model="editForm.description" placeholder="Description" class="border p-2 rounded col-span-2"></textarea>
          </div>
          <div class="mt-4 flex gap-4">
            <button type="submit" class="bg-green-600 text-white px-4 py-2 rounded hover:bg-green-700">Update</button>
            <button type="button" class="bg-gray-400 text-white px-4 py-2 rounded hover:bg-gray-500" @click="cancelEdit">Cancel</button>
          </div>
        </form>
      </div>

      <!-- Table -->
      <div class="p-4">
        <table class="w-full table-auto border border-gray-300">
          <thead class="bg-gray-800 text-white">
            <tr>
              <th class="border p-2 text-center w-12">
                <input type="checkbox" v-model="selectAll" @change="toggleAll" />
              </th>
              <th class="border p-2 text-left w-20 cursor-pointer" @click="sortById">
                <div class="flex items-center gap-1">
                  ID
                  <i class="fa-solid" :class="sortAsc ? 'fa-arrow-down' : 'fa-arrow-up'"></i>
                </div>
              </th>
              <th class="border p-2">Image</th>
              <th class="border p-2">Name</th>
              <th class="border p-2">Price</th>
              <th class="border p-2">Original Price</th>
              <th class="border p-2">Description</th>
              <th class="border p-2">Category</th>
              <th class="border p-2">createBy</th>
              <th class="border p-2">updateBy</th>
              <th class="border p-2">Action</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="product in sortedProducts" :key="product.id" class="text-center"
              :class="product.id % 2 === 0 ? 'bg-gray-100' : 'bg-white'">
              <td class="border p-2">
                <input type="checkbox" v-model="selected" :value="product.id" />
              </td>
              <td class="border p-2">{{ product.id }}</td>
              <td class="border p-2">
                <img :src="ImageURL + product.image_url" alt="..." class="h-16 mx-auto" />
              </td>
              <td class="border p-2">{{ product.name }}</td>
              <td class="border p-2">₭{{ product.price.toFixed(3) }}</td>
              <td class="border p-2">₭{{ product.original_price.toFixed(3) }}</td>
              <td class="border p-2">{{ product.description }}</td>
              <td class="border p-2">{{ product.category }}</td>
              <td class="border p-2">{{ product.createBy }}</td>
              <td class="border p-2">{{ product.updateBy }}</td>
              <td class="border p-2 text-blue-600 hover:underline cursor-pointer" @click="startEdit(product)">Edit</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import Sidebar from '~/components/sidebar.vue'

const base_url = 'http://192.168.69.246:4000'

const products = ref([])
const selected = ref([])
const selectAll = ref(false)
const sortAsc = ref(true)
const editingProduct = ref(null)
const editForm = ref({})
const keyword = ref('')

const fetchProducts = async () => {
  try {
    const response = await fetch(`${base_url}/getAll`)
    const data = await response.json()
    products.value = data.products || []
  } catch (error) {
    console.error('Failed to fetch products:', error)
  }
}
onMounted(fetchProducts)

const ImageURL = "http://192.168.69.246:4000/upload/show/"

function toggleAll() {
  selectAll.value ? selected.value = products.value.map(p => p.id) : selected.value = []
}

async function deleteProducts() {
  if (selected.value.length === 0) return alert('Please select at least one product to delete.')
  if (!confirm('Are you sure you want to delete these products?')) return

  try {
    const response = await fetch(`${base_url}/product/deleteMultiple`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ ids: selected.value })
    })

    if (response.ok) {
      alert('Products deleted successfully!')
      fetchProducts()
    } else {
      alert('Failed to delete products.')
    }

    selectAll.value = false
    selected.value = []
  } catch (error) {
    console.error('Error:', error)
  }
}

function sortById() {
  sortAsc.value = !sortAsc.value
}

const sortedProducts = computed(() =>
  [...products.value].sort((a, b) => sortAsc.value ? a.id - b.id : b.id - a.id)
)

function startEdit(product) {
  editingProduct.value = product.id
  editForm.value = { ...product }
}

function cancelEdit() {
  editingProduct.value = null
  editForm.value = {}
}

async function submitEdit() {
  try {
    const response = await fetch(`${base_url}/update/${editingProduct.value}`, {
      method: 'PUT',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(editForm.value)
    })

    if (response.ok) {
      alert('Product updated successfully')
      editingProduct.value = null
      editForm.value = {}
      fetchProducts()
    } else {
      alert('Failed to update product')
    }
  } catch (error) {
    console.error('Update error:', error)
  }
}

const searchProducts = async () => {
  if (!keyword.value.trim()) {
    fetchProducts()
    return
  }

  try {
    const res = await fetch(`${base_url}/search`, {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ keyword: keyword.value })
    })

    const data = await res.json()
    products.value = data.products || []
  } catch (err) {
    console.error("Search failed", err)
  }
}

</script>

<style scoped>
table {
  border-collapse: collapse;
}
</style>
