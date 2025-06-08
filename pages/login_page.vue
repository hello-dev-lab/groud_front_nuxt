<template>
  <div class="min-h-screen bg-[#2f2b3a] flex items-center justify-center p-4">
    <div class="bg-[#1f1c2e] bg-opacity-10 backdrop-blur-md border border-white border-opacity-20 p-8 rounded-xl w-full max-w-md text-white shadow-lg">
      <h2 class="text-3xl font-bold text-center mb-6 text-white">Login Form</h2>

      <form @submit.prevent="handleLogin">
        <!-- Email -->
        <div class="mb-4">
          <input
            v-model="email"
            type="email"
            placeholder="Enter your email"
            class="w-full bg-transparent border-b border-white py-2 px-1 focus:outline-none focus:border-blue-400"
            required
          />
        </div>

        <!-- Password + Toggle -->
        <div class="mb-4 relative">
          <input
            :type="showPassword ? 'text' : 'password'"
            v-model="password"
            placeholder="Enter your password"
            class="w-full bg-transparent border-b border-white py-2 px-1 pr-10 focus:outline-none focus:border-blue-400"
            required
          />
          <span
            @click="showPassword = !showPassword"
            class="absolute right-2 top-2 cursor-pointer text-sm text-blue-300"
          >
            {{ showPassword ? 'Hide' : 'Show' }}
          </span>
        </div>

        <!-- Remember / Forgot -->
        <div class="flex justify-between items-center text-sm mb-6">
          <label class="flex items-center space-x-2">
            <input type="checkbox" class="accent-blue-500" />
            <span>Remember me</span>
          </label>
          <a href="#" class="hover:underline text-blue-300">Forgot password?</a>
        </div>

        <!-- Submit Button -->
        <button
          type="submit"
          class="w-full bg-white text-black py-2 rounded hover:bg-gray-200 transition mb-4"
        >
          Log In
        </button>
      </form>

      <!-- Social Buttons -->
      <div class="flex gap-3 mb-4">
        <button class="p-1 bg-blue-600 hover:bg-blue-700 text-white py-2 rounded flex items-center justify-center gap-2">
          <span class="iconify text-xl" data-icon="mdi:facebook" />
          Continue with Facebook
        </button>
        <button class="p-1 bg-white hover:bg-gray-100 text-black py-2 rounded flex items-center justify-center gap-2">
          <span class="iconify text-xl" data-icon="mdi:google" />
          Continue with Google
        </button>
      </div>

      <!-- Register Link -->
      <p class="text-center text-sm">
        Don't have an account?
        <nuxt-link to="/register_page" class="text-blue-300 hover:underline">Register</nuxt-link>
      </p>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'

const email = ref('')
const password = ref('')
const showPassword = ref(false)
const router = useRouter()

const handleLogin = async () => {
  try {
    const response = await fetch('http://192.168.69.246:4000/admin/login', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        email: email.value,
        password: password.value,
      }),
    });

    const data = await response.json();

    if (response.status === 200) {
      localStorage.setItem('token', data.token);
      localStorage.setItem('adminId', data.admin.firstName);
      router.push('/dashboard');
    } else {
      alert(data.message || 'Failed to login');
    }
  } catch (error) {
    console.error('Login error:', error);
    alert('Login failed. Please check your server.');
  }
};

</script>

<script>
definePageMeta({
  head: {
    script: [
      {
        src: 'https://code.iconify.design/2/2.2.1/iconify.min.js',
        async: true,
      },
    ],
  },
})
</script>
