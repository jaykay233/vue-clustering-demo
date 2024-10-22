<template>
  <div class="flex flex-col h-screen bg-gray-100">
    <!-- Top Navigation -->
    <nav class="bg-gray-800 text-white p-4 shadow-md">
      <div class="container mx-auto flex items-center justify-between">
        <div class="text-2xl font-bold">Clustering Demo</div>
        <div class="flex space-x-4">
          <button v-for="item in navItems" :key="item" class="px-3 py-2 rounded-md text-sm font-medium hover:bg-gray-700 transition duration-150 ease-in-out">
            {{ item }}
          </button>
        </div>
        <div class="relative">
          <button @click="toggleUserMenu" class="flex items-center space-x-2 focus:outline-none">
            <span>{{ currentUser }}</span>
            <ChevronDown class="w-4 h-4" />
          </button>
          <div v-if="isUserMenuOpen" class="absolute right-0 mt-2 w-48 bg-white rounded-md shadow-lg py-1 z-50">
            <a href="#" class="block px-4 py-2 text-sm text-gray-700 hover:bg-gray-100">Profile</a>
            <a href="#" class="block px-4 py-2 text-sm text-gray-700 hover:bg-gray-100">Settings</a>
            <a href="#" class="block px-4 py-2 text-sm text-gray-700 hover:bg-gray-100">Logout</a>
          </div>
        </div>
      </div>
    </nav>

    <!-- Main Content -->
    <div class="flex flex-1 overflow-hidden">
      <!-- Left Sidebar -->
      <div class="w-64 bg-white p-4 overflow-y-auto shadow-md">
        <div class="mb-6">
          <h2 class="text-lg font-semibold mb-4">Data Source</h2>
          <div class="space-y-4">
            <div class="w-full">
              <label for="file-upload" class="block text-sm font-medium text-gray-700 mb-2">
                Upload file for analysis
              </label>
              <input
                  id="file-upload"
                  type="file"
                  class="w-full px-3 py-2 text-sm text-gray-700 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                  @change="handleFileUpload"
              />
            </div>
            <div v-if="uploadedFile" class="text-sm text-gray-600">
              File: {{ uploadedFile.name }}
            </div>
            <div v-if="fileAnalysisStatus" class="text-sm" :class="{'text-green-600': fileAnalysisStatus === 'Success', 'text-red-600': fileAnalysisStatus === 'Error'}">
              Status: {{ fileAnalysisStatus }}
            </div>
          </div>
        </div>
        <div>
          <h2 class="text-lg font-semibold mb-4">Topics</h2>
          <ul class="space-y-2">
            <li v-for="topic in topics" :key="topic.name" class="flex justify-between items-center text-sm text-gray-600">
              <span>{{ topic.name }}</span>
              <span class="bg-blue-100 text-blue-800 px-2 py-1 rounded-full text-xs">{{ topic.count }}</span>
            </li>
          </ul>
        </div>
      </div>

      <!-- Main Visualization Area -->
      <div class="flex-1 p-6 overflow-hidden">
        <div class="bg-white rounded-lg shadow-md p-6">
          <div class="flex justify-between items-center mb-6">
            <div class="flex space-x-2">
              <button v-for="view in viewTypes" :key="view.name"
                      class="flex items-center px-4 py-2 border border-gray-300 rounded-md text-sm font-medium hover:bg-gray-100 transition duration-150 ease-in-out">
                <component :is="view.icon" class="w-4 h-4 mr-2" />
                {{ view.name }}
              </button>
            </div>
            <div class="flex items-center space-x-2">
              <button class="flex items-center px-4 py-2 bg-blue-600 text-white rounded-md text-sm font-medium hover:bg-blue-700 transition duration-150 ease-in-out">
                <Search class="w-4 h-4 mr-2" />
                Analyze
              </button>
              <button class="px-4 py-2 border border-gray-300 rounded-md text-sm font-medium hover:bg-gray-100 transition duration-150 ease-in-out">
                Save View
              </button>
              <div class="relative">
                <button @click="toggleMoreMenu" class="flex items-center px-4 py-2 border border-gray-300 rounded-md text-sm font-medium hover:bg-gray-100 transition duration-150 ease-in-out">
                  More
                  <ChevronDown class="w-4 h-4 ml-2" />
                </button>
                <div v-if="isMoreMenuOpen" class="absolute right-0 mt-2 w-48 bg-white rounded-md shadow-lg py-1 z-40">
                  <a href="#" class="block px-4 py-2 text-sm text-gray-700 hover:bg-gray-100">Export</a>
                  <a href="#" class="block px-4 py-2 text-sm text-gray-700 hover:bg-gray-100">Share</a>
                  <a href="#" class="block px-4 py-2 text-sm text-gray-700 hover:bg-gray-100">Print</a>
                </div>
              </div>
            </div>
          </div>
          <div class="bg-gray-100 h-96 rounded-lg flex items-center justify-center">
            <p class="text-gray-500 text-lg">Visualization Area</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'
import { BarChart2, Map, Cloud, GitBranch, List, PieChart, Search, ChevronDown } from 'lucide-vue-next'

const navItems = ['Home', 'Search', 'History', 'Analysis', 'Learning Center']
const currentUser = ref('User')
const isUserMenuOpen = ref(false)
const isMoreMenuOpen = ref(false)
const uploadedFile = ref(null)
const fileAnalysisStatus = ref('')

const topics = [
  { name: 'Topic 1', count: 483 },
  { name: 'Topic 2', count: 149 },
  { name: 'Topic 3', count: 45 },
  { name: 'Topic 4', count: 102 },
  { name: 'Topic 5', count: 210 },
]

const viewTypes = [
  { name: 'Molecular', icon: BarChart2 },
  { name: 'Map', icon: Map },
  { name: 'Bubble', icon: Cloud },
  { name: 'Tree', icon: GitBranch },
  { name: 'Word Cloud', icon: List },
  { name: 'Matrix', icon: PieChart },
]

const toggleUserMenu = () => {
  isUserMenuOpen.value = !isUserMenuOpen.value
  if (isUserMenuOpen.value) {
    isMoreMenuOpen.value = false
  }
}

const toggleMoreMenu = () => {
  isMoreMenuOpen.value = !isMoreMenuOpen.value
  if (isMoreMenuOpen.value) {
    isUserMenuOpen.value = false
  }
}

const handleFileUpload = (event) => {
  const file = event.target.files[0]
  if (file) {
    uploadedFile.value = file
    fileAnalysisStatus.value = 'Analyzing...'
    setTimeout(() => {
      fileAnalysisStatus.value = 'Success'
    }, 2000)
  }
}

const closeMenus = (e) => {
  if (!e.target.closest('.relative')) {
    isUserMenuOpen.value = false
    isMoreMenuOpen.value = false
  }
}

onMounted(() => {
  document.addEventListener('click', closeMenus)
})

onUnmounted(() => {
  document.removeEventListener('click', closeMenus)
})
</script>