<template>
  <div class="flex min-h-screen">
    <!-- Sidebar -->
    <Sidebar />

    <!-- Main Content -->
    <div class="flex-1 p-6 bg-gray-100">
      <!-- Top Stats Cards -->
      <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4 mb-6">
        <CardStat title="All Earnings" value="$30,200" color="bg-orange-400" />
        <CardStat title="Page Views" value="290+" color="bg-green-400" />
        <CardStat title="Task Completed" value="145" color="bg-rose-400" />
        <CardStat title="Downloads" value="500" color="bg-cyan-400" />
      </div>

      <!-- Charts and Project Risk -->
      <div class="grid grid-cols-1 lg:grid-cols-3 gap-4 mb-6 bg-gray-100">
        <div class="lg:col-span-2 bg-white rounded shadow p-4">
          <h2 class="font-bold mb-2">Sales Analytics</h2>
          <Linechart />
        </div>
        <div class="bg-white rounded shadow p-4">
          <h2 class="font-bold mb-2">Project Risk</h2>
          <div class="flex items-center justify-center h-48 text-orange-600 font-bold text-4xl">
            5
          </div>
          <p class="text-center text-gray-600 mt-24">Balanced</p>
          <button class="w-full mt-4 bg-orange-400 hover:bg-orange-500 text-white py-2 px-4 rounded">
            Download Overall Report
          </button>
        </div>
      </div>

      <!-- Application Sales + User Activity -->
      <div class="grid grid-cols-1 lg:grid-cols-3 gap-4">
        <div class="lg:col-span-2 bg-white rounded shadow p-4">
          <h2 class="font-bold mb-2">Application Sales</h2>
          <table class="w-full text-sm">
            <thead>
              <tr class="text-left text-gray-500 border-b">
                <th class="py-2">Application</th>
                <th class="py-2">Sales</th>
                <th class="py-2">Avg Price</th>
                <th class="py-2">Total</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="item in apps" :key="item.name" class="border-b">
                <td class="py-2">{{ item.name }}</td>
                <td class="py-2">{{ item.sales }}</td>
                <td class="py-2">{{ item.price }}</td>
                <td class="py-2 text-green-500">{{ item.total }}</td>
              </tr>
            </tbody>
          </table>
        </div>
        <div class="bg-white rounded shadow p-4">
          <h2 class="font-bold mb-2">User Activity</h2>
          <div v-for="n in 4" :key="n" class="flex items-start gap-2 mb-3">
            <img src="https://i.pravatar.cc/40?img=1" class="rounded-full w-8 h-8" />
            <div>
              <p class="font-semibold">John Deo</p>
              <p class="text-xs text-gray-500">Lorem Ipsum is simply dummy text.</p>
              <p class="text-xs text-gray-400">2 min ago</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import Sidebar from '~/components/sidebar.vue'

const apps = [
  { name: 'Able Pro', sales: '16,300', price: '$53', total: '$15,652' },
  { name: 'Gurubale', sales: '8,265', price: '$98', total: '$9,652' },
  { name: 'Gurubale', sales: '8,265', price: '$98', total: '$9,652' },
  { name: 'Photoshop', sales: '26,421', price: '$35', total: '$18,785' },
  { name: 'Gurubale', sales: '8,265', price: '$98', total: '$9,652' },
  { name: 'Photoshop', sales: '26,421', price: '$35', total: '$18,785' },
  { name: 'Gurubale', sales: '8,265', price: '$98', total: '$9,652' },
  { name: 'Gurubale', sales: '8,265', price: '$98', total: '$9,652' },
  { name: 'Photoshop', sales: '26,421', price: '$35', total: '$18,785' },
  { name: 'Gurubale', sales: '8,265', price: '$98', total: '$9,652' },
  { name: 'Flatable', sales: '10,652', price: '$20', total: '$7,856' }
]
</script>
