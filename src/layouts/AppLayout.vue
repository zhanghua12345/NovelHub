<template>
  <VaLayout
    :top="{ order: 2 }"
    :left="{ absolute: breakpoints.mdDown, order: 1, overlay: breakpoints.mdDown && !isSidebarMinimized }"
    @leftOverlayClick="isSidebarMinimized = true"
  >
    <template #top>
      <AppNavbar :is-mobile="isMobile" />
    </template>

    <template #left>
      <div class="md:mt-[24px] md:h-[340px] md:rounded-main overflow-hidden">
        <AppSidebar :minimized="isSidebarMinimized" :animated="!isMobile" :mobile="isMobile" />
      </div>
    </template>

    <template #content>
      <div class="overflow-x-hidden overflow-y-auto w-full pt-[24px]" :style="{ height: 'calc(100vh - 84px)' }">
        <main class="p-4 pt-0">
          <article>
            <RouterView />
          </article>
        </main>
      </div>
    </template>
  </VaLayout>
</template>

<script setup>
import { onBeforeUnmount, onMounted, ref } from 'vue'
import { storeToRefs } from 'pinia'
import { onBeforeRouteUpdate } from 'vue-router'
import { useBreakpoint } from 'vuestic-ui'

import { useGlobalStore } from '../stores/global-store'

import AppNavbar from '../components/navbar/AppNavbar.vue'
import AppSidebar from '../components/sidebar/AppSidebar.vue'

const GlobalStore = useGlobalStore()

const breakpoints = useBreakpoint()

const sidebarWidth = ref('16rem')
const sidebarMinimizedWidth = ref(undefined)

const isMobile = ref(false)
const isTablet = ref(false)
const { isSidebarMinimized } = storeToRefs(GlobalStore)

const onResize = () => {
  isSidebarMinimized.value = breakpoints.mdDown
  isMobile.value = breakpoints.smDown
  isTablet.value = breakpoints.mdDown
  sidebarMinimizedWidth.value = isMobile.value ? '0' : '4.5rem'
  sidebarWidth.value = isTablet.value ? '100%' : '16rem'
}

onMounted(() => {
  window.addEventListener('resize', onResize)
  onResize()
})

onBeforeUnmount(() => {
  window.removeEventListener('resize', onResize)
})

onBeforeRouteUpdate(() => {
  if (breakpoints.mdDown) {
    // Collapse sidebar after route change for Mobile
    isSidebarMinimized.value = true
  }
})
</script>

<style lang="scss" scoped>
// Prevent icon jump on animation
.va-sidebar {
  width: unset !important;
  min-width: unset !important;
}

/* 隐藏滚动条轨道 */
::-webkit-scrollbar {
  width: 0px; /* 对于垂直滚动条 */
  height: 0px; /* 对于水平滚动条 */
}

/* 隐藏滚动条滑块 */
::-webkit-scrollbar-thumb {
  background: transparent;
}
</style>
