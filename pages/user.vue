<template>
  <div class="flex min-h-screen">
    <Sidebar />

    <div class="p-6 bg-gray-100 flex-1">
      <h1 class="text-2xl font-bold mb-6 text-gray-800">👥 User Management</h1>

      <!-- Search Input -->
      <div class="mb-4">
        <input v-model="searchTerm" type="text" placeholder="Search users by name or email"
          class="w-full p-2 border border-gray-300 rounded" />
      </div>

      <button @click="openAddUserDialog" class="bg-green-500 text-white px-4 py-2 rounded mb-4 hover:bg-green-600">
        + Add User
      </button>

      <!-- User Table -->
      <div class="bg-white shadow rounded-lg overflow-x-auto">
        <table class="min-w-full text-sm text-gray-700">
          <thead>
            <tr class="bg-gray-200 text-left text-gray-600 uppercase text-xs">
              <th class="px-4 py-3">#</th>
              <th class="px-4 py-3">Username</th>
              <th class="px-4 py-3">Email</th>
              <th class="px-4 py-3">Created At</th>
              <th class="px-4 py-3">Actions</th>
            </tr>
          </thead>
          <tbody>
            <tr v-if="filteredUsers.length === 0">
              <td colspan="5" class="text-center py-4 text-gray-500">
                🔍 No users found
              </td>
            </tr>
            <tr v-for="(user, index) in filteredUsers" :key="index" class="border-b hover:bg-gray-50 transition">
              <td class="px-4 py-2">{{ index + 1 }}</td>
              <td class="px-4 py-2 font-medium">{{ user.userName }}</td>
              <td class="px-4 py-2">{{ user.email }}</td>
              <td class="px-4 py-2">{{ formatDate(user.createdAt) }}</td>
              <td class="px-4 py-2 space-x-2">
                <button @click="editUser(user)"
                  class="bg-blue-500 text-white px-3 py-1 rounded hover:bg-blue-600">Update</button>
                <button @click="deleteUser(user.id)"
                  class="bg-red-500 text-white px-3 py-1 rounded hover:bg-red-600">Delete</button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <!-- Add User Dialog -->
      <div v-if="isAddUserDialogOpen" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center">
        <div class="bg-white p-6 rounded-lg w-96">
          <h2 class="text-xl font-semibold mb-4">Add New User</h2>
          <div>
            <label class="block text-sm font-medium text-gray-700">Username</label>
            <input v-model="newUser.userName" type="text" class="w-full p-2 border border-gray-300 rounded mb-4"
              placeholder="Enter username" />
          </div>
          <div>
            <label class="block text-sm font-medium text-gray-700">Email</label>
            <input v-model="newUser.email" type="email" class="w-full p-2 border border-gray-300 rounded mb-4"
              placeholder="Enter email" />
          </div>
          <div>
            <label class="block text-sm font-medium text-gray-700">Password</label>
            <input v-model="newUser.password" type="password" class="w-full p-2 border border-gray-300 rounded mb-4"
              placeholder="Enter password" />
          </div>
          <div class="flex justify-end space-x-4">
            <button @click="closeAddUserDialog" class="bg-gray-500 text-white px-4 py-2 rounded hover:bg-gray-600">
              Cancel
            </button>
            <button @click="addUser" class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600">
              Add User
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import axios from 'axios'

const API_BASE = 'http://192.168.69.246:4000'

const users = ref([])
const searchTerm = ref('')
const isAddUserDialogOpen = ref(false)
const newUser = ref({ userName: '', email: '', password: '' })

// Fetch users from backend
const fetchUsers = async () => {
  try {
    const response = await axios.get(`${API_BASE}/user/getAll`)
    console.log('Users fetched:', response.data.users)
    users.value = response.data.users
  } catch (error) {
    console.error('Failed to load users:', error)
  }
}

// Format date safely
const formatDate = (dateStr) => {
  if (!dateStr) return 'N/A'
  const date = new Date(dateStr)
  return isNaN(date) ? 'Invalid Date' : date.toLocaleString()
}

// Computed filtered users
const filteredUsers = computed(() => {
  const search = searchTerm.value.toLowerCase()
  return users.value.filter(user =>
    user.userName.toLowerCase().includes(search) ||
    user.email.toLowerCase().includes(search)
  )
})

// Open dialog
const openAddUserDialog = () => {
  isAddUserDialogOpen.value = true
}

// Close dialog
const closeAddUserDialog = () => {
  isAddUserDialogOpen.value = false
  newUser.value = { userName: '', email: '', password: '' }
}

// Add user
const addUser = async () => {
  const { userName, email, password } = newUser.value
  if (userName && email && password) {
    try {
      await axios.post(`${API_BASE}/user`, newUser.value)
      await fetchUsers()
      closeAddUserDialog()
    } catch (error) {
      console.error('Failed to add user:', error)
    }
  } else {
    alert('Please fill in all fields.')
  }
}

// Delete user
const deleteUser = async (id) => {
  const confirmed = confirm('Are you sure you want to delete this user?')
  if (confirmed) {
    try {
      await axios.delete(`${API_BASE}/user/${id}`)
      await fetchUsers()
    } catch (error) {
      console.error('Failed to delete user:', error)
    }
  }
}

// Edit user
const editUser = async (user) => {
  const newUserName = prompt('Enter new username:', user.userName)
  const newEmail = prompt('Enter new email:', user.email)

  if (newUserName && newEmail) {
    try {
      await axios.put(`${API_BASE}/user/${user.id}`, {
        userName: newUserName,
        email: newEmail
      })
      await fetchUsers()
    } catch (error) {
      console.error('Failed to update user:', error)
    }
  }
}

onMounted(fetchUsers)
</script>

<style scoped>
/* Add extra styling if needed */
</style>
