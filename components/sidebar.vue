<template>
  <div class="w-64 bg-gray-800 text-white h-screen p-4 space-y-4">
    
    <div>
      <h1 class="text-xl font-bold mb-4">🧩 Admin Panel</h1>
    </div>
    <ul class="space-y-2">
      <li>
        <NuxtLink to="/dashboard" class="block hover:bg-gray-700 p-2 rounded">
          Dashboard
        </NuxtLink>
      </li>
      <li class="hover:bg-gray-700 p-2 rounded cursor-pointer">Analytics</li>
      <li>
        <NuxtLink to="/products" class="block hover:bg-gray-700 p-2 rounded">
          Products
        </NuxtLink>
      </li>

      <li>
        <NuxtLink to="/category" class="block hover:bg-gray-700 p-2 rounded">
          Category
        </NuxtLink>
      </li>

      <li>
        <NuxtLink to="/user" class="block hover:bg-gray-700 p-2 rounded">
          Users
        </NuxtLink>

      </li>

      <li>
        <nuxt-link to="/pages/setting_page.vue" class="block hover:bg-gray-700 p-2 rounded">setting</nuxt-link>
      </li>
    </ul>
  </div>
</template>
