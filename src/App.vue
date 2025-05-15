<script setup>
import { useRoute } from 'vue-router'
import { ref, onMounted, onBeforeUnmount } from 'vue'
import Sidebar from './components/Sidebar.vue'
import HeaderNav from './components/HeaderNav.vue'
import BottomButton from './components/BottomButton.vue'

const route = useRoute()
const showSidebar = ref(true)

function handleScroll() {
  if (route.path === '/') {
    // 스크롤이 800px 이상 내려가면 사이드바 표시
    showSidebar.value = window.scrollY > 800
  } else {
    showSidebar.value = true
  }
}

onMounted(() => {
  window.addEventListener('scroll', handleScroll)
  handleScroll()
})

onBeforeUnmount(() => {
  window.removeEventListener('scroll', handleScroll)
})
</script>

<template>
  <Sidebar v-if="showSidebar" />
  <HeaderNav />
  <router-view :key="$route.fullPath" />
  <BottomButton />
</template>
