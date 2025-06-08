<template>
  <div class="min-h-screen bg-[#2f2b3a] flex items-center justify-center p-4">
    <div class="bg-[#1f1c2e] rounded-2xl shadow-2xl flex flex-col md:flex-row w-full max-w-5xl overflow-hidden">
      
      <!-- Left: Image and text -->
      <div class="md:w-1/2 bg-cover bg-center p-8 text-white flex flex-col justify-between">
        <div class="flex justify-between items-center">
          <h2 class="text-lg font-bold">AMU</h2>
          <a href="#" class="bg-white text-black px-3 py-1 rounded text-sm">Back to website</a>
        </div>
        <div>
            <img src="https://png.pngtree.com/png-vector/20230728/ourmid/pngtree-code-clipart-character-illustration-with-cartoon-boy-and-computer-vector-png-image_6805624.png" alt="">
        </div>
        <div class="mt-auto">
          <h1 class="text-2xl font-semibold mb-2">Capturing Moments, Creating Memories</h1>
          <div class="flex gap-2 mt-4">
            <div class="w-2 h-2 rounded-full bg-white opacity-50"></div>
            <div class="w-2 h-2 rounded-full bg-white opacity-50"></div>
            <div class="w-2 h-2 rounded-full bg-white"></div>
          </div>
        </div>
      </div>

      <!-- Right: Form -->
      <div class="md:w-1/2 bg-[#1f1c2e] text-white p-8">
        <h2 class="text-2xl font-bold mb-1">Create an account</h2>
        <p class="text-sm mb-6">
          Already have an account?
          <nuxt-link to="/login_page" class="text-blue-300 hover:underline">Login</nuxt-link>
        </p>

        <form @submit.prevent="submit" class="space-y-4">
          <div class="flex gap-4">
            <input v-model="form.firstName" type="text" placeholder="First name" class="w-1/2 bg-[#2b283a] rounded p-2 focus:outline-none" />
            <input v-model="form.lastName" type="text" placeholder="Last name" class="w-1/2 bg-[#2b283a] rounded p-2 focus:outline-none" />
          </div>
          <input v-model="form.email" type="email" placeholder="Email" class="w-full bg-[#2b283a] rounded p-2 focus:outline-none" />
          <input v-model="form.password" type="password" placeholder="Enter your password" class="w-full bg-[#2b283a] rounded p-2 focus:outline-none" />

          <label class="flex items-center text-sm space-x-2">
            <input type="checkbox" required class="accent-purple-500" />
            <span>I agree to the Terms & Conditions</span>
          </label>

          <button type="submit" class="w-full bg-purple-600 hover:bg-purple-700 py-2 rounded font-semibold">
            Create account
          </button>
        </form>

        <!-- Social buttons -->
        <div class="mt-6">
          <div class="flex justify-center gap-3">
            <button class="bg-[#2b283a] text-white py-2 px-4 rounded flex items-center gap-2 hover:bg-[#3b3750]">
              <Icon name="mdi:google" class="text-xl" />
              Google
            </button>
            <button class="bg-[#2b283a] text-white py-2 px-4 rounded flex items-center gap-2 hover:bg-[#3b3750]">
              <Icon name="mdi:apple" class="text-xl" />
              Apple
            </button>
          </div>
        </div>
      </div>

    </div>
  </div>
</template>

<script setup>

import { useRouter } from 'vue-router'
const router = useRouter()


const form = ref({
  firstName: '',
  lastName: '',
  email: '',
  password: '',
})

const base_url = 'http://192.168.69.246:4000'


const submit = async() => {
  try {
    const response = await fetch(`${base_url}/admin`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(form.value)
    })

    if(response.status === 201) {
      alert('Admin added successfully')
      router.push('/login_page')

    }else {
      alert('Failed to add admin')
    }
  } catch (error) {
    alert('Error:', error)
  }
}
</script>