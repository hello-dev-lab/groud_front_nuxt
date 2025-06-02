<template>
    <div class="flex min-h-screen">
        <!-- Sidebar -->
        <Sidebar />

        <!-- Main Content -->
        <div class="flex-1 p-6 bg-gray-100">
            <!-- Top Header Section -->
            <div class="bg-gray-700 text-white p-6 rounded mb-6 flex justify-between items-center">
                <h1 class="text-2xl font-bold">Category</h1>
                <div class="flex items-center space-x-4">
                    <div class="relative">
                        <input type="search" placeholder="Search category"
                            class="p-2 pl-8 rounded bg-gray-600 text-white border border-gray-500" v-model="keyword"
                            @input="searchCategory" />
                        <i class="fa-solid fa-magnifying-glass absolute left-2 top-2.5 text-gray-300"></i>
                    </div>
                    <i class="fa-solid fa-user text-xl"></i>
                </div>
            </div>

            <!-- Add Category Button -->
            <div class="mb-4">
                <button class="bg-blue-500 hover:bg-blue-600 text-white px-4 py-2 rounded">
                    <NuxtLink to="/addCategory">+ Add Category</NuxtLink>
                </button>
            </div>

            <!-- Edit Form -->
            <div v-if="editingCategory" class="bg-white p-4 mb-4 rounded shadow">
                <h2 class="text-lg font-bold mb-2">Edit Category</h2>
                <form @submit.prevent="submitEdit">
                    <input v-model="editForm.name" placeholder="Category Name"
                        class="p-2 border rounded w-full mb-2" />
                    <div class="flex gap-4">
                        <button type="submit"
                            class="bg-green-600 text-white px-4 py-2 rounded hover:bg-green-700">Update</button>
                        <button type="button" class="bg-gray-400 text-white px-4 py-2 rounded"
                            @click="cancelEdit">Cancel</button>
                    </div>
                </form>
            </div>

            <!-- Category List Table -->
            <div class="grid grid-cols-1 gap-4">
                <div class="bg-white rounded shadow p-4">
                    <h2 class="font-bold text-lg mb-4">All Categories</h2>
                    <table class="w-full text-sm">
                        <thead>
                            <tr class="text-left text-gray-500 border-b">
                                <th class="py-2">#</th>
                                <th class="py-2">Category Name</th>
                                <th class="py-2">Image</th>
                                <th class="py-2">Created At</th>
                                <th class="py-2 text-center">Actions</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="(category, index) in categories" :key="category.id"
                                class="border-b hover:bg-gray-50">
                                <td class="py-2">{{ index + 1 }}</td>
                                <td class="py-2">{{ category.categoryName }}</td>
                                <td class="py-2 text-center">
                                    <img :src="ImageURL + category.image_url" alt="Category Image" class="w-20 h-16" />
                                </td>
                                <td class="py-2">{{ category.createdAt }}</td>
                                <td class="py-2 text-center space-x-2">
                                    <button class="text-blue-500 hover:underline"
                                        @click="startEdit(category)">Edit</button>
                                    <button class="text-red-500 hover:underline"
                                        @click="deleteCategory(category.id)">Delete</button>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import Sidebar from '~/components/sidebar.vue'

interface Category {
    id: number
    categoryName: string
    image_url: string
    createdAt: string
}

const categories = ref<Category[]>([])
const editingCategory = ref<number | null>(null)
const editForm = ref<{ name: string }>({ name: '' })
const keyword = ref('')
const ImageURL = "http://192.168.69.246:4000/upload/show/"
const base_url = "http://192.168.69.246:4000"

const fetchCategories = async () => {
    try {
        const response = await fetch(`${base_url}/category/getAll`)
        const data = await response.json()
        categories.value = data.categories || []
    } catch (error) {
        console.error("Fetch failed:", error)
    }
}

onMounted(fetchCategories)

const searchCategory = async () => {
    try {
        if (keyword.value.trim() === '') {
            fetchCategories()
            return
        }
        const res = await fetch(`${base_url}/category/search`, {
            method: "POST",
            headers: { "Content-Type": "application/json" },
            body: JSON.stringify({ keyword: keyword.value })
        })
        const data = await res.json()
        categories.value = data.categories || []
    } catch (err) {
        console.error("Search error:", err)
    }
}

const startEdit = (category: Category) => {
    editingCategory.value = category.id
    editForm.value = { name: category.categoryName }
}

const cancelEdit = () => {
    editingCategory.value = null
    editForm.value = { name: '' }
}

const submitEdit = async () => {
    try {
        const res = await fetch(`${base_url}/category/update/${editingCategory.value}`, {
            method: 'PUT',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify(editForm.value)
        })

        if (res.ok) {
            alert("Category updated successfully!")
            await fetchCategories()
            cancelEdit()
        } else {
            alert("Failed to update category.")
        }
    } catch (error) {
        console.error("Update failed:", error)
    }
}

const deleteCategory = async (id: number) => {
    if (!confirm("Are you sure you want to delete this category?")) return

    try {
        const res = await fetch(`${base_url}/category/delete/${id}`, {
            method: 'DELETE'
        })

        if (res.ok) {
            alert("Category deleted successfully!")
            await fetchCategories()
        } else {
            alert("Failed to delete category.")
        }
    } catch (error) {
        console.error("Delete failed:", error)
    }
}
</script>

<style scoped>
input:focus {
    outline: none;
}
</style>
